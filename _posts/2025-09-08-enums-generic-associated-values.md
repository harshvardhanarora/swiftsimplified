---
title: Swift enums with generic associated values
description: 
date: 2025-09-08 00:00:00
categories: [Swift Generics]
tags: [swift, enums, generics]
---

If you often work with enums, you must have come across situations where you need to have cases with generic associated values. Let's consider an example of an enum `Travel` -

```swift
enum Travel {
    case road
    case train
    case air
}
```

Now right now it is pretty straight forward that you can travel through 3 different routes. Let's consider a case where we also have a `Car` structure and we wanted to be able to have our road travel be able to specify a car.

```swift
struct Car {
    let range: Int
}
```

Now let's update `Travel` - 

```swift
enum Travel {
    case road(Car)
    case train
    case air
}
```

That works well for this case. What would happen if we have more than one type of vehicle that we want to specify? Let's say we also had a `Bus` -

```swift
struct Bus {
    let range: Int
}
```

Now our road travel cannot accommodate both the modes of transport without any changes to it. That's where generics come in. Generics allow us to not have to specify the concrete type in the definition and let it get inferred based on where it is instantiated from. Let's see how that would look like -

```swift
enum Travel<T> {
    case road(T)
    case train
    case air
}

let travelByCar = Travel.road(Car(range: 300))
let travelByBus = Travel.road(Bus(range: 500))
```

Based on how they are instantiated, Swift compiler will assign a concrete type to `travelByCar` and `travelByBus` of their respective type based on the value assigned to them. Eg. `travelByBus` would be of the type Travel<Bus>.

Now let's say we wanted to make `Travel` equatable, we won't get that for free since we now have an associated value that is a generic.

```swift
enum Travel<T>: Equatable {
    case road(T)
    case train
    case air
}
```
>  Type `Travel<T>` does not conform to protocol `Equatable`
{: .prompt-danger}

The simplest way to make this go away is to ensure that our generic `T` always conform to the `Equatable` protocol. This would mean that anytime we create any `Travel` object, we would need to specify the generic type that is equatable. This is how it would look like - 

```swift
enum Travel<T: Equatable>: Equatable {
    case road(T)
    case train
    case air
}

struct Car: Equatable {
    let range: Int
}

let travelByTrain = Travel<Car>.train
```

What if we don't want to always do that? What if we only want to supply an `Equatable` type if we actually want to be able to compare that enum? Let's see what we could do to get that to work with a `Walk` struct which will not conform to `Equatable`.

```swift
struct Walk { }

enum Travel<T> {
    case road(T)
    case train
    case air
}

extension Travel: Equatable where T: Equatable { }

let travelByTrain = Travel<Walk>.train
```

Now what this extension does is that only make `Travel` equatable if our generic type `T` also is equatable. Let's see that in action -

```swift
let travelByTrain = Travel<Car>.train
let travelByAir = Travel<Car>.air
print(travelByTrain == travelByAir)
```

This would give you `false` but no error - since `Car` is equatable this also making `Travel` equatable. Let's now try comparing similar enums when we use `Walk` instead of `Car` as the generic type - 

```swift
let travelByTrain = Travel<Walk>.train
let travelByAir = Travel<Walk>.air
print(travelByTrain == travelByAir)
```

> Operator function `==` required that `Walk` conform to `Equatable`
{: .prompt-danger}

Hopefully this has helped you understand how generics work with enums and how we can have conditional protocol conformances on the type itself based on the inferred type of the generic value associated with the enum.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.