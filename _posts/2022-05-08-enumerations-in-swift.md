---
title: Enumerations in Swift
description: 
date: 2022-05-08 00:00:00
categories: [Swift Basics]
tags: [swift, enums]
---
Enumerations in Swift allow you to group related values under a common type which enables us to write type-safe code.

**Common Questions —**
- How to create Enums?
- How to add **properties** and **methods** to Enums?
- How to add custom **initialisation** for Enums?
- How to conform Enums to **protocols**?
- How to write **extensions** for Enums?

These are the questions I’ll be looking to answer in this article.

---

#### How to define Enums in Swift?

```swift
enum Color {
    case red
    case blue
    case green
    case yellow
    case purple
}
```

You just created an enum called `Colors`. Inside your enum, you create `cases` that are the possible values your enum can take. In this example, we create 5 difference cases for different colors. You can create as many as you need!

> Notice how we name the enum `Color` and not `Colors`. This is done intentionally keeping in mind that a value of this type can only be set to a single case.

We can also write the cases in a single line —

```swift
enum Vehicle {
    case bike, car, bus, truck
}
```

#### How to initialise an Enum?

```swift
var personalVehicle = Vehicle.bike
let officeVehicle = Vehicle.car
```

Here we create two properties, one variable and a constant. Both are of type Vehicle. Swift can automatically infer the type of the property, so now we can change our variable to something else without specifying `Vehicle` —

`personalVehicle = .truck`

#### Comparing Enum types

Comparison of enum types in not a simple topic, so I will break it down and explain it as we go. Till now we have only looked at the most basic way of creating enums. Enums with cases created this way are automatically comparable, as you can see from this example —

```swift
let firstVehicle = Vehicle.car
let secondVehicle = Vehicle.car
print(firstVehicle == secondVehicle)
// Output: true
```

As we add more things to our Enums, comparing cases will not be so much straightforward

#### Matching Enum Values

Now suppose you have a property of an enum type, and you need to perform certain operation based on which case it belongs to. One option would be to compare that property to each individual case —

```swift
func doSomething(with vehicle: Vehicle) {
    if vehicle == .bike {
        print("This is a bike")
    } else if vehicle == .car {
        print("This is a car")
    } else if vehicle == .bus {
        print("This is a bus")
    } else if vehicle == .truck {
        print("This is a truck")
    }
}
```

But it doesn’t look very neat does it? Also, there is a possibility you might miss out a case in certain scenarios. Let’s look at how we can use Switch statement to do this better —

```swift
func doSomethingBetter(with vehicle: Vehicle) {
    switch vehicle {
    case .bike:
        print("This is a bike")
    case .car:
        print("This is a car")
    case .bus:
        print("This is a bus")
    case .truck:
        print("This is a truck")
    }
}
```

#### Iterating over Enum cases

It is often useful to iterate over all the cases of an enum type. Let’s look at how we can do that —

```swift
enum Direction: CaseIterable {
    case north, south, east, west
}

let allDirectionsCount = Direction.allCases.count
print(allDirectionsCount)
// Output - 4
```

---

### Associated Values

As of right now, you can assign your enum case to a constant or variable and access it later in your code. But what if you wanted to store more information along with your enum cases? This is where **associated values** comes in. Let’s try to see it with an example —

```swift
enum Travel {
    case car(distance: Int)
    case train(Int)
    case flight(airportName: String, travelHours: Double)
}

let travelByCar = Travel.car(distance: 560)
let travelByTrain = Travel.train(500)
let travelByFlight = Travel.flight(airportName: "Heathrow", travelHours: 1.5)
```

Now you must be wondering, how does the `switch` statement would work with enum cases with associated values? Let’s see with an example —

```swift
func checkTravel(for travel: Travel) {
    switch travel {
    case .car:
        print("Travelling by Car")
    case .train(let distance):
        print("Travelling \(distance)km by Train")
    case .flight(airportName: var airportName, _):
        airportName += "Airport"
        print("Travelling by Flight from \(airportName)")
    }
}
```

Now I have tried to cover different things with each case here, let’s see it case by case —

1. Even though the `car` case has an associated type `Int` named distance with it, we can chose to ignore it
2. The `train` case has an associated type `Int` but has no name, but we can still assign it a name while iterating over the enum type.
**NOTE**: The name `distance` will only be valid inside the scope of the case statement.
3. The `flight` case has two associated types — a `String` with name `airportName` and a `Double` named `travelHours`. If you look inside the switch statement, we get the `airportName` as a `variable` instead of a `constant` and we chose to ignore the `travelHours` completely.

This covers most of the cases you will see/use for associated values. Now we will look at another concept for enums, which allows us to have default values for the enum case itself.

---

### Raw Values

We saw how we can have associated values with an enum type. An alternative to that is to have default values (of same type) for each case. Let’s see it in an example —

```swift
enum RomanNumber: String {
    case one = "I"
    case two = "II"
    case three = "III"
    case four = "IV"
}
```

Now let’s see how you would access these raw values —

```swift
let romanTwo = RomanNumber.two
print(romanTwo.rawValue)
// Output - II

```

In the above example, we have explicitly specified the raw value for each of our cases. Swift is also capable of implicitly assign raw values to our type. Let’s look at an example —

```swift
enum Number: Int {
    case one = 1
    case two
    case three
    case four
}

let numberThree = Number.three
print(numberThree.rawValue)
// Output - 3

```

Now even though you did not specify the raw value for `three`, Swift automatically inferred it to be `3` since you started with `1`. If you did not specify any value for the case `one`, Swift would have set the cases as 0, 1, 2, 3 automatically.

#### Initialising Enum from Raw Value

You can initialise an enum from its raw value. The initialiser is a **failable** initialiser. You will learn about this in the subsequent articles, but for now you can assume that it will either successfully return the enum if it can, otherwise return nil if it was unable to create an enum from the specified raw value. Let’s look at it with an example —

```swift
let romanFour = RomanNumber(rawValue: "IV")!
print(romanFour.rawValue)
// Output - IV
```

I use the “!” here because I know the initialiser will be successful because my raw value exists in the enum. You should avoid using this in your code as it would lead to a crash if the enum could not be initialised with that raw value.

#### Comparing Enums (Continued…)

We looked at comparing enums at the beginning which had no associated values or raw values attached to them. But now after looking at the complexity that enums bring along, you must be wondering how do you compare enums if they have associated or raw values?

> Enums can only have either associated value or raw value but not both at the same time.

Enum’s with raw values can be compared exactly like enums without raw values. Since raw values for each case are constants and do not change, it does not impact how we compare those enums.

```swift
let numberOne = Number.one
let anotherNumberOne = Number.one
print(numberOne == anotherNumberOne)
```

Now the issue is how do we compare enums with associated values? Let’s look at an example —

```swift
enum Animal {
    case land(name: String, numberOfLegs: Int)
    case water(name: String, numberOfFins: Int)
    case air(name: String, numberOfWings: Int)
}

let horse = Animal.land(name: "Horse", numberOfLegs: 4)
let zebra = Animal.land(name: "Zebra", numberOfLegs: 4)
```

Now you would naturally have to urge to simple try to compare them using the “==” operator. Try it out! Let’s see what happens —

`print(horse == zebra)`

You’d also have an error saying — `Binary operator '==' cannot be applied to two 'Animal' operands`

This is because Swift does not know how to compare these enums. Even though it might look obvious to you that these are not the same, Swift is not aware how to reach that conclusion.

Change your enum Animal declaration to this —

```swift
enum Animal: Equatable {
    case land(name: String, numberOfLegs: Int)
    case water(name: String, numberOfFins: Int)
    case air(name: String, numberOfWings: Int)
}
```

The error goes away and returns you the output false.
This is known as conforming to a `protocol`. You will learn more about this in the upcoming articles, but for the time being you can think of it this way — Now Swift knows how to compare two instances of our `Animal` type.

---
That’s it! That was quite a journey we covered with a lot of important concepts. Hopefully you now have a good working understanding of how enums work in Swift. Feel free to reach out to me in case you have any questions. Do follow my page to receive more articles on Swift fundamentals.

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.