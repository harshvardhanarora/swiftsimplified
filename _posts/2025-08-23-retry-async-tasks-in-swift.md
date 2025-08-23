---
title: Retry Async tasks in Swift
description: Learn how to implement retry logic for async tasks in Swift using async-await, with practical examples, generic solutions, and thorough testing. Improve reliability of your network calls and async operations with flexible retry strategies.
date: 2025-08-23 00:00:00
categories: [Concurrency]
tags: [swift, concurrency, async-await, generics]
---
If you use async-await for your network tasks, you'll often encounter situations where you want to retry a task a certain number of times before allowing it to fail. Sometimes, it's not just a single request you need to retry, and the need for flexibility grows as your codebase expands. Today, we'll build a simple function to handle such requests, making it easy to specify different retry counts for different tasks. Let's get started!

## Basic Example: Retrying an Async Network Call

Let's start with a simple scenario. Suppose you have an async function that fetches an integer from a network request:

```swift
func fetchNumber() async throws -> Int {
    // Imagine this makes a network call and returns an Int
    Int.random(in: 1...100)
}
```

Now, let's say you want to try this request up to 3 times before giving up if it fails. The most straightforward way is to use a for loop:

```swift
func fetchWithRetries() async throws -> Int {
    for attempt in 0..<3 {
        do {
            return try await fetchNumber()
        } catch {
            if attempt == 2 {
                throw error // Only throw after 3 failed attempts
            }
            // Otherwise, try again
        }
    }
    throw NSError(domain: "com.example", code: 1) // Should never reach here
}
```

This pattern works, but it's not very flexible if you want to change the number of retries or reuse it for different tasks.

---

## Making It Generic: A Flexible Retry Function

To make this reusable, let's create a generic function that can retry any async task. We'll call it `retryingAsync`. It will take the number of retries and a closure representing the async task:

```swift
func retryingAsync<T>(retries: Int, task: @escaping () async throws -> T) async throws -> T {
    for attempt in 0..<retries {
        do {
            return try await task()
        } catch {
            if attempt == retries - 1 {
                throw error
            }
            // Otherwise, try again
        }
    }
    throw NSError(domain: "com.example", code: 1)
}
```

Now you can use this function for any async task, not just network calls:

```swift
let value = try await retryingAsync(retries: 3) {
    try await fetchNumber()
}
```

This approach makes your code much cleaner and easier to maintain, especially as your codebase grows and you need to retry different kinds of async operations.

---

## Testing the Retry Logic

Even though this looks simple, it's important to test it thoroughly. Let's create a helper that lets us control when the task should fail or succeed, so we can write predictable tests. This ensures your retry logic behaves exactly as expected in all scenarios.

Here's a mock helper:

```swift
struct MockAsyncTask {
    var results: [Bool] // true = success, false = failure
    var callCount = 0
    
    mutating func run() async throws -> String {
        callCount += 1
        guard !results.isEmpty else { throw TestError.alwaysFail }
        let shouldSucceed = results.removeFirst()
        if shouldSucceed {
            return "Success"
        } else {
            throw TestError.alwaysFail
        }
    }
    
    enum TestError: Error { case alwaysFail }
}
```

Let's write a few tests:

**Test 1: Happy Path (Succeeds on First Try)**
```swift
var mock = MockAsyncTask(results: [true])
let result = try await retryingAsync(retries: 3) {
    try await mock.run()
}
assert(result == "Success")
assert(mock.callCount == 1)
```

**Test 2: All Retries Fail**
```swift
var mock = MockAsyncTask(results: [false, false, false])
try? await retryingAsync(retries: 3) {
    try await mock.run()
}
assert(mock.callCount == 3)
```

**Test 3: Succeeds After Several Retries**
```swift
var mock = MockAsyncTask(results: [false, false, true])
let result = try await retryingAsync(retries: 5) {
    try await mock.run()
}
assert(result == "Success")
assert(mock.callCount == 3)
```

With this approach, you can be confident that your retry logic works as expected in all scenarios, and your async code remains robust and reliable.

---

## Conclusion

Retrying async tasks is a common need, especially for network operations. By abstracting the retry logic into a generic function, you make your code more flexible and easier to test. Always make sure to test your retry logic with different scenarios to avoid surprises in production. With these patterns, your Swift code will be more resilient and easier to maintain.

---
Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.