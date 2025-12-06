---
title: Async Sequences in Swift
description: Learn how to use AsyncSequence in Swift to handle asynchronous streams of data with ease. This guide explains what AsyncSequence is, why it’s essential for Swift concurrency, and how to implement it in real-world iOS apps. With practical examples and best practices, you’ll discover how AsyncSequence simplifies asynchronous programming and makes your code cleaner, more efficient, and scalable.
date: 2025-12-06 00:00:00
categories: [Concurrency]
tags: [swift, asyncsequence, concurrency]
---

`AsyncSequence` is a key part of Swift concurrency, enabling developers to work with asynchronous streams efficiently. [SE-0298](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0298-asyncsequence.md) introduced `AsyncSequence` building on top of the async-await proposal in their push towards structured concurrency. This article de-constructs and simplifies the to make it easy to digest for those new to iOS development.


## Motivation
In the last article, we explored Swift [async-await](../introduction-async-await) and how it helps us eliminate complicated and error-prone completion handlers and allows us to do asynchronous tasks in a single line. In doing that, we allow for a function to be able to suspend itself while it is executing a `async` task and free up the thread for other tasks if needed. But once the function completes all its tasks and returns the result to the user, it's job is done and it will not produce any more results.

We want the function to be able to do more. We want the function to be able to return something we can iterate over, and the values are received asynchronously as they come and not as a single result. Something likes this -

```swift
for try await image in myDownloader.images() {
    // do something with each image
}
```
The idea would be that we continue to get images until our downloader throws an error or returns `nil` at which point the loop breaks. We could potentially break the look ourself if certain conditions are met within the loop.

## How AsyncSequence Works in Swift

### Definition

Let's look at how `Sequence` right now works, you have the protocol itself which has a method that returns an iterator. `AsyncSequence` has been built on the same foundation.

#### Iterator Protocol

```swift
public protocol AsyncIteratorProtocol {
    associatedtype Element

    mutating func next() async throws -> Element?
}
```

The protocol defines a generic type `Element` and a function which would return our generic as an *optional*. 

##### Why the `mutating`, `async` and `throws`?
It's simple, all the keywords in the protocol definition can be satisfied by a function implementation that uses none of them. But if the protocol definition did not have any of those keywords and we wanted our actual function to be mutating or async (although won't make sense using `AsyncIterator` and not having this as `async`) or throwing, it would fail to satisfy the protocol definition and throw an error.

#### AsyncSequence Protocol

```swift
public protocol AsyncSequence {
    associatedtype Element
    associatedtype AsyncIterator: AsyncIteratorProtocol where AsyncIterator.Element == Element

    func iterator() -> AsyncIterator
}
```
After the iterator protocol, this is pretty straight forward. The protocol simply defines a generic type `Element` similarly to how the iterator protocol did.

> **Note:** The `Element` keyword in both the protocol is a generic used within the scope of that protocol, it does not imply that both are the same.
{: .prompt-info}

It also defines a generic iterator that conforms to our previous protocol, with a condition that the inferred generic of the `AsyncSequence` should be of the same type as that of the iterator protocol. This makes sense because we would want our iterator to emit the same type of element which the sequence is expected to emit.

### Implementing AsyncSequence in Swift

The protocol defines how our sequence type should look like. Let's do some examples to see this in practice.

```swift
struct Numbers: AsyncSequence {
    let min: Int
    let max: Int

    struct NumberProvider: AsyncIteratorProtocol {
        let min: Int
        let max: Int
        func next() -> Int? {
            return Int.random(in: min...max)
        }
    }

    func makeAsyncIterator() -> NumberProvider {
        NumberProvider(min: min, max: max)
    }
}

for await num in Numbers(min: 1, max: 10) {
    print(num)
}
```
 
This a basic implementation of our protocols. I did not make the method `mutating`, `async` or `throwing` to show you what I meant earlier about the significance of those in the protocol. We still had to use `await` at the calling place to allow the compiler to differentiate between iterating over a `Sequence` vs `AsyncSequence`.

### Design

In the above example, the compiler will do the following under the hood which makes it work -

```swift
let iterator = Numbers(min: 1, max: 10).makeAsyncIterator()
while let num = await iterator.next() {
    print(num)
}
```

In this interpretation, `await` is not mandatory because the actual `next()` method on our particular iterator is not `async`. 

#### Cancellation

Swift proposal for structured concurrency discusses [Task cancellation here](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0298-asyncsequence.md#cancellation). You can check for cancellation using the `Task` API at which stage you should either throw and error or return `nil` signalling that there will no more elements emitted by the sequence. If there is any cleanup activities you need to perform, they can be done after checking for cancellation or in the `deinit` for *class* types.

#### End of Iteration

After the sequence returns `nil` or throws an error from the `next()` method, all the future calls must return `nil`. This is important because it is the only way to signal that the sequence will not emit any more items.

### AsyncSequence Functions

Creation of the `AsyncSequence` protocol enabled us to write generic functions for types that conform to it. These functions can either return a single value similar to how `min`, `max`, `reduce` work or a new `AsyncSequence` itself similar to how `map`, `filter` work.

We'll take an example of how an extension to get the minimum value of an `AsyncSequence` will look like. It will only work on `Element`'s which conform to `Comparable`.

```swift
extension AsyncSequence where Element: Comparable {
    public func min() async throws -> Element? {
        var result: Element?
        for try await element in self {
            if let m = result {
                result = Swift.min(m, element)
            } else {
                result = element
            }
        }
        return result
    }
}
```

This is a manual implementation to just give an understanding how to go about it doing it yourself. This was an example of `AsyncSequence` returning a single value, returning an `AsyncSequence` will work quite similarly.

You don't need to go about implementing the basic extensions that you use on `Sequence` types, Swift has provided some default function extensions on `AsyncSequence` as a part of this (proposal)[https://github.com/swiftlang/swift-evolution/blob/main/proposals/0298-asyncsequence.md#asyncsequence-to-single-value]. Go check them out and try it out in a playground to get your head around it.