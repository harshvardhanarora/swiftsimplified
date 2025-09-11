---
title: Swift enums with generic associated values
description: 
date: 2025-09-08 00:00:00
categories: [Swift Generics]
tags: [swift, enums, generics]
---

If you often work with enums, you’ve likely needed cases with associated values that can vary by type.  Let’s model a scenario where a generic associated value represents the load state of some data.

### A non-generic starting point

```swift
enum LoadState {
    case idle
    case loading
    case loaded(String)
    case failed
}
```

This works if you always load a String. It breaks as soon as you need to load other types (Int, User, etc.).

### Make it generic where it matters

We can make the payload generic and keep the same states:

```swift
enum LoadState<T> {
    case idle
    case loading
    case loaded(T)
    case failed
}

struct User { let id: Int }

let number: LoadState<Int> = .loaded(42)
let name: LoadState<String> = .loaded("Running")
let user: LoadState<User> = .loaded(User(id: 1))
```

Here, the generic `T` is “what is being loaded.” For example, `LoadState<User>.loading` means “a user is loading.”

### Equatable and generic associated values

If we try to make `LoadState` conform to `Equatable` directly, the compiler can’t synthesize it because `T` might not be equatable:

```swift
enum LoadState<T>: Equatable {
    case idle
    case loading
    case loaded(T)
    case failed
}
```
> Type `LoadState<T>` does not conform to protocol `Equatable`
{: .prompt-danger}

#### Option 1: Require `T` to be Equatable at the type declaration

```swift
enum LoadState<T: Equatable>: Equatable {
    case idle
    case loading
    case loaded(T)
    case failed
}

struct User: Equatable { let id: Int }

let a = LoadState<Int>.loaded(1)
let b = LoadState<Int>.loaded(2)
print(a == b) // false
```

This works, but it forces every `LoadState` to use an `Equatable` payload, even when you don’t need equality.

#### Option 2: Conditional conformance only when needed

Keep `LoadState` generic without constraints, and add `Equatable` only when `T` is `Equatable`:

```swift
enum LoadState<T> {
    case idle
    case loading
    case loaded(T)
    case failed
}

extension LoadState: Equatable where T: Equatable { }
```

Now you get equality for free only when it makes sense:

```swift
struct User: Equatable { let id: Int }
let u1 = LoadState<User>.loaded(User(id: 1))
let u2 = LoadState<User>.loaded(User(id: 2))
print(u1 == u2) // false
```

And if your payload isn’t equatable, equality isn’t available—which is exactly what we want:

```swift
struct Session { }
let s1 = LoadState<Session>.idle
let s2 = LoadState<Session>.idle
print(s1 == s2)
```

> Operator function `==` requires that `Session` conform to `Equatable`
{: .prompt-danger}

This keeps intent clear and avoids extra constraints, while still letting the compiler synthesize `Equatable` when it can.

I hope this shows how to design enums with generic associated values, and how conditional conformance keeps code simple and clear.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.