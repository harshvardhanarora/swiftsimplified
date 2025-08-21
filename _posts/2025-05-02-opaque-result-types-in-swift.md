---
title: Opaque Result Types in Swift
description: Understand Swift's opaque result types (some Type) - purpose, how they hide concrete types while preserving API flexibility, and practical examples.
date: 2025-05-02 00:00:00
categories: [Swift Generics]
tags: [swift, generics]
---
Apple has made significant improvements to their generics model. This article will help you understand one part of it: opaque result types. We’ll look at why they were created and how you can use them in your code.

Opaque result types were introduced by Apple in Swift 5.1. You can read the proposal about them [here](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0244-opaque-result-types.md).

---

## Motivation
First, we’ll explore the motivation behind introducing opaque types. To illustrate this, we’ll start with an example using generics. Imagine we want to define a protocol for a `Vehicle`, we would define it as follows:

```swift
protocol Vehicle {
    func drive()
}
```

Any type that conforms to this protocol must implement the drive() function. Now, let's create some concrete classes that conform to this protocol.

```swift
struct Car: Vehicle {
    func drive() {
        print("I am driving a car.")
    }
}

struct Boat: Vehicle {
    func drive() {
        print("I am driving a boat.")
    }
}
```

Often, we encounter situations where we want to combine our vehicles into a more complex type. Consider the following structure:

```swift
struct Vehicles<A: Vehicle, B: Vehicle>: Vehicle {
    var a: A
    var b: B

    func drive() {
        a.drive()
        b.drive()
    }
}
```

This `Vehicles` structure holds two vehicle objects while still conforming to the `Vehicle` protocol Vehicle itself. Now that we have defined our vehicles, let’s define a protocol for a `Trip`:

```swift
protocol Trip {
    associatedtype TripVehicle: Vehicle

    var vehicle: TripVehicle { get }
}
```

Our `Trip` protocol includes an `associatedtype`, `TripVehicle`, that conforms to the `Vehicle` protocol. You might be wondering why we needed an `associatedtype` here instead of simply having the `vehicle` property conform to `Vehicle`? The issue with the latter approach is that it would make the property an existential, leading to various trade-offs as discussed [here](https://forums.swift.org/t/improving-the-ui-of-generics/22814). Now, let’s create a trip that conforms to our protocol.

```swift
struct Bali: Trip {
    var vehicle: Vehicles<Car, Boat> {
        return Vehicles(a: Car(), b: Boat())
    }
}
```

At this point, the return type may seem straightforward and easy to write, so it might not appear to be a concern. However, keep in mind that our `Vehicles` structure could become much more complex, resulting in a lengthy and complicated return type for the `vehicle` property. The issue with this is that we should not have to explicitly type the entire structure since the return type only needs to conform to `Vehicle`.

Another problem is that the client receiving this Bali trip now knows the concrete type of the `vehicle` property and might ending up relying on that assumption. If you were to change your `Vehicles` structure in the future and introduce more vehicles, it could break the client code because they built their code around the concrete type that we exposed.

```swift
let trip = Bali()
let vehicle = trip.vehicle // vehicle will be of type Vehicles<Car, Boat>
```

## Solution

As we observed in the previous example, it was neither necessary nor advisable to explicitly declare the specific return type of our vehicle in the Bali trip. Our intention was simply to indicate that the return type conforms to `Vehicle`. Apple, with their intuitive approach to nomenclature, introduced the syntax `some Vehicle` for this purpose. Let's see how our type would look now:

```swift
struct Bali: Trip {
    var vehicle: some Vehicle {
        return Vehicles(a: Car(), b: Boat())
    }
}

let trip = Bali()
let vehicle = trip.vehicle // vehicle will be of type some Vehicle
```

This allows us to declare that the `vehicle` property is of type `Vehicle` without specifying the exact type of vehicle. Unlike with existential types, we do not completely lose type identity. Although the client sees a `Vehicle`, the compiler still knows its concrete type. We will explore this soon.

### Type Identity

When functions return opaque types, different calls to the same function must return the same concrete type. To better understand this concept, let’s extend our `Vehicle` protocol to include a `speed` property and make the protocol `Equatable`.

```swift
protocol Vehicle: Equatable {
    var speed: Int { get }
    func drive()
}
```

Now, consider the following function:

```swift
func getVehicle() -> some Vehicle {
    let car1 = Car(speed: Int.random(in: 1...50))
    let car2 = Car(speed: Int.random(in: 1...50))
    return car1.speed >= car2.speed ? car1 : car2
}
```

This is valid because the concrete return type remains the same under both conditions. Although the output of this function is of type `some Vehicle`, unlike existential types, we can still compare different vehicles returned by this function. Let's take a look:

```swift
let vehicle1 = getVehicle()
let vehicle2 = getVehicle()
print(vehicle1 == vehicle2) // this is okay
```

This should work! However, there are cases where the opaque result type will depend on the generic arguments provided when the function is called. This means that the return type of the same function can vary based on different arguments. Let’s consider this example:

```swift
protocol Animal {
    associatedtype FoodType

    var food: FoodType { get }
}

struct GenericAnimal<T>: Animal {
    var food: T
}

func createAnimal<T>(food: T) -> some Animal {
    GenericAnimal(food: food)
}

let animal1 = createAnimal(food: "a")
let animal2 = createAnimal(food: "b")
print(animal1 == animal2) // error
```

In this case, the concrete type returned by our createAnimal function cannot be determined at compile time. As a result, different outputs from this function will not be considered statically equivalent to the type it happens to be bound to.

#### Notes  
⭐️ Opaque result types can be used with properties and subscripts.  
⭐ Opaque result types cannot be used in requirement of a protocol.  
⭐ Opaque result types cannot be used for non-final class declarations.  

That’s it! That was a lot of ground to cover. Give yourself time to absorb all of it, try to write out the code yourself. Feel free to reach out to me on LinkedIn in case you have any questions.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.