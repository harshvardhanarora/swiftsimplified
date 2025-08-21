---
title: Structures and Classes in Swift
description: 
date: 2022-05-15 00:00:00
categories: [Swift Basics]
tags: [swift, struct, class]
---
Structures and Classes make the building blocks of your program. They’re capable of storing data and functionality in the form of constants, variables and functions. This articles covers a broad range of topics from understanding what are structs and classes and why do we need them, how to create them, how to add data and functionality and how to access that data and functionality. Let’s go!

---

#### Why Structs and Classes?
If you are coming from another programming language, you’re already familiar with the need of data encapsulation. But let’s try to explain it for others in a very simple manner. Consider you want to track a few animals in your code, and you want to track their type, name and weight. This is how it would look like for 2 such examples —

```swift
let animalOneType = "Dog"
let animalOneName = "Marco"
let animalOneWeight = 45

let animalTwoType = "Cat"
let animalTwoName = "Ruby"
let animalTwoWeight = 24.5
```

You should be able to spot 2 issues with this —
1. Difficult to maintain. For every animal you have to maintain 3 properties. With increasing number of animals to maintain, you’d have 3 times the number of properties.
2. Type of `animalOneWeight` will be inferred as `Int` while that of `animalTwoWeight` will be `Double`. This can cause bugs in the code later on which you would have to come back and fix for all the wrong entries.

Wouldn’t it be great to have a single container for each animal that we wanted to create? That container would store all the information that we needed.
Let’s see how this would look like in a `Structure`.

---

#### Create and Access Structures

```swift
struct Animal {
    let type: String
    let name: String
    let weight: Double
}

let animalOne = Animal(type: "Dog", name: "Marco", weight: 45.0)
print(animalOne.name)
// Output - Marco
```

We learnt **three** things here —
1. How to **define a struct**
2. How to **create an instance of a struct**
3. How to **access properties of a struct**

If this looks simple, that’s because it actually is. It is THAT simple to define, create and access structures (similarly classes as well).

#### Free Initialiser in Structs

Before we move forward, take a look at line 21 above. Although we know that it instantiates a structure for us, but how does that happen? Is there a method that we cannot see that does that for us?

Yes, and that method is called **initialiser**. In case of structures, Swift provides us with a free initialiser that lets us instantiate all the stored properties of our struct right when we are creating one.

`let animalTwo = Animal.init(type: "Cat", name: "Ruby", weight: 24.5)`

As you can see, this time we directly call the **init** method that the structure provided to us without us having to do anything. It is not necessary to do this because Swift automatically infers that we are trying to call the **initialiser** of the struct.

I know it doesn’t seem very clear how **initialisation** works and that’s alright because it will be covered in detail in subsequent articles. For now, you only need to understand that `structures` provide us with a **free initialiser** which helps us create an instance of that struct.

It is important to note here that you still can create a custom initialiser for a struct as well in which case the **free initialise**r would not longer be valid.

---

#### Create and Access Class

The difference in creating a class from a struct is that we do not receive a **free initialiser** when creating a class. So the definition would look like —

```swift
class Car {
    let name: String
    let modelYear: Int
    let weight: Double
    
    init(name: String, modelYear: Int, weight: Double) {
        self.name = name
        self.modelYear = modelYear
        self.weight = weight
    }
}
```

There’s a lot more to know and understand about initialisers which we will dedicate an entire article to later on.

Creating and accessing the class properties is exactly like struct —

```swift
let defender = Car(name: "Defender", modelYear: 2020, weight: 2345.6)
print(defender.name)
// Output - Defender
```

---

#### Difference between Struct and Class

Most you must be aware that classes are reference types while struct are value types. Let’s try to understand that with an example —

```swift
struct DimensionStruct {
    var width: Int
    var height: Int
    
    init(side: Int) {
        self.width = side
        self.height = side
    }
}
```

Here we define a structure with a custom initialiser. Now try this —

`let shape = DimensionStruct(width: 5, height: 7)`


You will receive an error as this is not a valid initialiser anymore. Swift only provides you a free initialisers if you have not created any.

Let’s create a class as well now —

```swift
class DimensionClass {
    var width: Int
    var height: Int
    
    init(width: Int, height: Int) {
        self.width = width
        self.height = height
    }
}
```

No surprises here! Not lets create instances of both of them —

```swift
let dimensionStructOne = DimensionStruct(side: 5)
let dimensionClassOne = DimensionClass(width: 6, height: 7)
```

Now what we will do is, create two more properties, assign them to above one’s, try to change their values and observe what happens. That seems like a lot of work but its pretty straightforward —

```swift
var dimensionStructTwo = dimensionStructOne
var dimensionClassTwo = dimensionClassOne

dimensionStructTwo.width = 10
dimensionClassTwo.width = 10

print(dimensionStructOne.width)
// Output - 5
print(dimensionClassOne.width)
// Output - 10
```

**Observation 1** — Changing the **width** property of **dimensionStructTwo** did not impact the width property of **dimensionStructOne**.
**Observation 2** — Changing the **width** property of **dimensionClassTwo** also changed the width property of **dimensionClassOne**.

That’s the simplified version of the difference between reference type and value type. Value types properties actually hold that values they store while reference properties hold a reference to where the data is actually stored.

When assign a struct to another property, it creates a copy of that struct. While in the case of assigning a class to another property, simply the reference to the data stored is transferred to the new property which points to the same data that the original property points to.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.