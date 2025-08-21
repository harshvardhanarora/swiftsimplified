---
title: Thread Safety with Subjects in RxSwift
description: Practical explanation of scheduler behaviour and thread-safety when using RxSwift Subjects, with examples and safe patterns for updating subjects from multiple threads.
date: 2022-03-27 00:00:00
tags: [swift, rxswift]
---
On which thread does your RxSwift chain execute its code? How do you perform costly operations without blocking the main thread? What happens if multiple threads try to update your subject at the same time? Let's dive into these questions.

#### Which thread does an RxSwift chain perform its code?

It is a common misunderstanding that by default any code inside an Rx chain is executed on background threads. That’s not true.
The documentation for RxSwift Schedulers says —

> In short, if no explicit scheduler is chosen, those methods will be called on current thread/scheduler.

That means, if you do not specify a scheduler manually, and you call any methods inside your chain, those methods will be executed on the the thread you initiate that chain from.

Let’s look at our first method in the code —

```swift
let subject = BehaviorSubject<Int>(value: 0)

print("Start")

subject
    .do(onNext: { _ in
        print("A")
        Thread.sleep(forTimeInterval: 5)
        print("B")
    })
.subscribe(onNext: { print($0) })
.disposed(by: disposeBag)

print("End")
```
We will start with calling this method in `viewDidAppear(_:)`. If you run the app and wait for a few seconds, the output you see will be —

```
Start
A
B
0
End
```

That’s not what you’d expect to happen! You do not get the `Exit` log until after the subject has finished executing its code. Now try another thing — Run the app again and tap the button quickly a couple of times and look at the log.

You immediately get `Start` and `A`, but you do not receive anything else. After the 5 seconds, this is what your final output will be —

```
Start
A
B
0
End
Button is responsive
Button is responsive
```

Your button was not responsive while the code inside the subject was executing. It was not until after that block executed, that your UI inputs were processed.

So now you understand what to look out for while creating subjects. If you are planning on calling costly methods from your subject, do not do it from the main thread.

---

That brings us to our next question, how do you perform these costly operations inside subjects?

Short answer — Schedulers. We’ll directly look at them with an example, but if you are interesting in understanding them a little bit more, feel free to checkout their documentation [here](https://github.com/ReactiveX/RxSwift/blob/main/Documentation/Schedulers.md)!

Let’s look at the second method in the code —

```swift
let subject = BehaviorSubject<Int>(value: 0)

print("Start")

subject
    .subscribe(on: ConcurrentDispatchQueueScheduler.init(qos: .background))
    .do(onNext: { _ in
        print("A")
        Thread.sleep(forTimeInterval: 5)
        print("B")
    })
.subscribe(onNext: { print($0) })
.disposed(by: disposeBag)

print("End")
```

What we do here is that we set our subject to run on a background thread using the `subscribe(on:)` method. We can also make use of observe(on:) method here but explaining these methods would be out of scope for this article. But if you are interesting in understanding them, here is an amazing article for it!

Try running the second experiment again — Run the app and quickly tap the button twice. This will be the output —

```
Start
End
A
Button is responsive
Button is responsive
B
0
```

You receive `Start`, `End` and `A` quickly on launching the app. You also receive the 2 button events from your tapping the button twice. And after a few seconds, you receive `B` and `0`. That’s how it should be!

---

At this point, you understand how to manipulate where you subject execute its code. But what happens if the subject itself is updated from different threads at the same time?

Let’s look at an example. Here is the third method in the code —

```swift
let subject = PublishSubject<Int>()
let deadline: DispatchTime = .now() + .seconds(5)

subject
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)

let dispatch1 = DispatchQueue(label: "Dispatch 1", qos: .userInitiated)
let dispatch2 = DispatchQueue(label: "Dispatch 2", qos: .userInitiated)

dispatch1.asyncAfter(deadline: deadline) {
    subject.onNext(5)
}

dispatch2.asyncAfter(deadline: deadline) {
    subject.onNext(10)
}
```

What we do here is create two separate [dispatch queues](https://developer.apple.com/documentation/dispatch/dispatchqueue) and use them to update the subject at the same time.

Now in your output, you will notice the that after the delay, the output 5 and 10 will be printed in a random order along with a long error printed right in the middle of them.

What’s happening here is that both dispatch queue fetch separate threads to execute the code that is asked of them. The subject received the second value before it has completed processing the first one which is the cause for this warning.

Now in almost all cases you will receive this warning (and not a crash) because it is extremely rare for both threads to update the methods on the exact same time. Even though this is not crashing the app in the most cases, it is still a bad coding practise to update subjects this way. It might cause unexpected behaviours or even crashes at rare instances.

#### How to address this problem?

We created two different dispatchers to mock up a situation where you might have two different network calls fetching some data and trying to update the subject with that information.

To fix that, we need to update the subject on a common thread. What this basically means is, the network calls will still be made on different threads, but when they receive their data, we would use that data to update our subject using a `Serial Dispatcher`. You can read about concurrent and serial dispatchers [here](https://www.avanderlee.com/swift/concurrent-serial-dispatchqueue/)!

Let’s jump into some code now to understand this. Here’s the last method in the code —

```swift
let subject = PublishSubject<Int>()
let deadline: DispatchTime = .now() + .seconds(5)

subject
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)

let dispatch1 = DispatchQueue(label: "Dispatch 1", qos: .userInitiated)
let dispatch2 = DispatchQueue(label: "Dispatch 2", qos: .userInitiated)
let dispatch3 = DispatchQueue(label: "Serial Dispatch")

dispatch1.asyncAfter(deadline: deadline) {
    dispatch3.async {
        subject.onNext(5)
    }
}

dispatch2.asyncAfter(deadline: deadline) {
    dispatch3.async {
        subject.onNext(10)
    }
}
```

What’s different here is that we create a serial dispatcher and use that to update our subject from within our previous dispatchers.

Now this would return you `5` and `10` (not always in that order) without any errors. This is because the serial dispatcher waits for the first request to complete before jumping onto the next one.

So if you have multiple network calls running on separate threads and they are trying to update a common subject (true for other objects as well), this would be a clean way to ensure your code does not throw any warnings or even crash in certain rare scenarios.

Now I understand that was a lot to take in, but we have finally got to the end. At this point, you should be comfortable in understanding how scheduling works in Rx and what are the best practises to undertake. Feel free to reach out to me if you have any questions!

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.