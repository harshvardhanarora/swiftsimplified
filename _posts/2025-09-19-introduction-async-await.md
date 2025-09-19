---
title: Introduction to Async-Await in Swift
description: My notes covering basics of async-await
date: 2025-09-19 00:00:00
categories: [Concurrency]
tags: [swift, async-await, concurrency]
---

[SE-296](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0296-async-await.md) introduced `async-await` to the Swift language which is a part of the structured concurrency that Apple has been trying to push towards.

## Motivation
We as developers often have been using closures and completion handlers to do asynchronous programming. This gets complicated if you need to execute a sequence of operations that rely on the result of a previous operation. You will end up with a nested loop of completion handlers and error handling becomes a nightmare.

## Solution
The solution is having asynchronous functions that would allow for functions to be written as straight-line synchronous code.

Function types can simply me marked as `async` indicating that the function is asynchronous:

```swift
func loadData() async { ... }
```

Even a class initializer could be asynchronous:

```swift
func MyClass {
    init() async { ... }
}
```

> `deinit` cannot be `async`
{: .prompt-info}

### Computed Properties and Subscripts
When this proposal came out, initially there was no plan to support `async` storage accessors (i.e. `get`, `set`). But then as a part of [SE-310](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0310-effectful-readonly-properties.md), it was allowed to add `aync`, `throw` or both of these effects to **read-only** computed properties or subscripts. So you could write something like -

```swift
class StoreManager {
    var topSale: Int {
        get async throws {
            // fetch and return
        }
    }

    // Sales for the date
    subscript(_ day: Date) -> [Int] {
        get async throws {
            // fetch and return
        }
    }
}
```

### Function Types
Every function in **Swift** has a type, it might take in a/some values and return something. Async functions types are different than their sync counterparts as well. But there is implicit conversion from a sync function to an async function but not the other way round. So if you had a property the type of which is an async function, you would assign a sync function to it. But if you had a property which is a sync function, assigning an async function to it would be an error. This is similar to how `throw` works as well. I'll share the same example that the proposal itself uses to explain it the best -

```swift
struct FunctionTypes {
  var syncNonThrowing: () -> Void
  var syncThrowing: () throws -> Void
  var asyncNonThrowing: () async -> Void
  var asyncThrowing: () async throws -> Void
  
  mutating func demonstrateConversions() {
    asyncNonThrowing = syncNonThrowing
    asyncThrowing = syncThrowing
    syncThrowing = syncNonThrowing
    asyncThrowing = asyncNonThrowing
    
    syncNonThrowing = asyncNonThrowing // error
    syncThrowing = asyncThrowing       // error
    syncNonThrowing = syncThrowing     // error
    asyncNonThrowing = syncThrowing    // error
  }
}
```

An *effect* is an observable behavior of a function. `throws` indicates that a function can fail with an `Error` and `async` indicates that the function might get suspended at some point during execution. 

Now think of what we just discussed above in this way - every time the function type getting assigned has the same or less number of effects than the property type it is getting assigned to - we are good. If the function type getting assigned has more or different number of effects - it will be an error. 

Thankfully you don't have to worry about it too much since Swift compiler will give you an error telling you exactly what's going wrong with your code. With this concept in mind, you would be able to understand why you are getting that error.

### Closures
A closure can also have an `async` function type -

```swift
let closure = { () async -> Void in
    await asyncNonThrowing()
}
```

Even if you don't specify the closure type as `async`, but still make an `await` call inside it, Swift will implicitly infer it as an `async` closure. This is still valid - 

```swift
let closure = { () -> Void in
    await asyncNonThrowing()
}

let closure2 = { await asyncNonThrowing() }
```

An important thing to note here is that in case of nested functions or closures, this `async` inference does not propagates outside the closure or function itself. Consider this example - 

```swift
let closure = { () -> Void in          // not async
    let closure2 = { () -> Void in     // implicitly async
        await asyncNonThrowing()
    }
}
```

### Overloading
A lof of developers use completion handlers to handle the completion of a function. Now let's look at how we would go about creating `async` versions of different functions. Take a look at the following functions - 

```swift
func doSomethingSimple() { }
func doSomething(with input: String) { }
func doSomethingElse(completionHandler: (() -> Void)? = nil) { }
```

Now let's try creating the `async` version of these functions by overloading them - 

```swift
func doSomething(with input: String) { }
func doSomething(with input: String) async { }
func doSomethingElse(completionHandler: (() -> Void)? = nil) { }
func doSomethingElse() async { }
```

Now the first case was straight forward, we simply added the word `async` in front of the `()` brackets. The second one had a completion handler which was to be called once the function was completed. Given how `async` functions have sequential execution, we would no longer need to have a completion handler so we removed that parameter and simply added the `async` word. Now let's try to see how we would call these methods and how Swift would be able to differentiate which method we are referring to -

```swift
doSomethingSimple()         // calls the sync method
await doSomethingSimple()   // calls the async method

doSomethingElse { print("Do something else") }
await doSomethingElse()
```

Now in both these cases, it was pretty straight forward for Swift to understand which method we are trying to call between `sync` and the `async` one. But what if we did not provide anything for the completion handler in the second case and left it to the default `nil`?

```swift
doSomethingElse()
```

Now before this proposal, the default Swift behavior to handle overloading is to pick the function with the fewer default arguments, which in this case would throw an error since Swift would be trying to infer this as the user trying to call the `async` method.


As a part of this proposal, there was an overload-solution rule brought in to solve this problem which would select the appropriate function based on the context it is getting called from. If the calling function is in a `sync` context, it will pick the `sync` function and vice-versa for `async` function. So the above example that we had, `doSomethingElse()` would be inferred as the `sync` version. Let's look at what would happen if we do this in an `async` context - 

```swift
func testSomething() async {
    doSomethingElse()       // error - Expression is `async` but is not marked with `await`
}
```

In this case, `doSomethingElse()` is getting called from an `async` context, so Swift will infer it as the `async` method while picking the correct overload. Now because of that it will expect it to be called with the `await` keyword which is reflected in the error as well.

### Autoclosures

A function cannot take an autoclosure parameter of `async` type unless the function itself is `async`. This is merely to preserve semantics and not create confusion. The why of this is not important, just something to keep in mind. Also, even if you forget this, the Swift compiler will throw an error if you ever make this mistake.

That's it for a basic introduction to async-await. This is a powerful tool in the structured concurrency box that will make your nested code simple, clean and easier to understand. 

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.