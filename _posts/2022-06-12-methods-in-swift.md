---
title: Methods in Swift
description: 
date: 2022-06-12 00:00:00
categories: [Swift Basics]
tags: [swift, methods]
---
You’ve learnt about **functions**, and you’ve learnt about data types like **structs**, **classes** and **enums**. Methods are simply functions that are associated with a type. Similar to properties, we can have instance methods and type methods.

### Instance Methods

As the word suggests, these methods belong to instances of your class, struct or enum.

```swift
class Company {
    var numberOfEmployees = 0
    
    func addSomeEmployees(number: Int) {
        numberOfEmployees += number
    }
}
```

As you can see, the syntax for creating instance methods is exactly same as creating functions. The only difference being a method is always inside a type.

#### self

Every instance of a type has a property called self that refers to the instance itself. You can use that property inside instance methods of that type. You don’t need to use this normally, as you can see from the above example when we refer to `numberOfEmployee` inside out method. So when would you need it? Let’s take a look at an example —

```swift
func newNumberOfEmployees(numberOfEmployees: Int) {
    self.numberOfEmployees = numberOfEmployees
}
```
> Note: The above method was defined inside the Company class defined above

Inside the method, the parameter name takes precedence over properties defined in the class. Thus if you did not use `self`, Swift would assume you are trying to modify the parameter itself instead of the instance property. (which will throw an error)


#### Value type vs Reference type

You already understand the difference between value types (structs and enums) and reference types (classes) from the article on [Structs and Classes](../structures-and-classes-in-swift). Now there is an important difference to understand here.

By default, instance properties of value types cannot be modified by instance methods. The same is not true for reference type, where instance methods can modify instance properties.

We need to add mutating keyword to that method to allow it to change instance properties of that type. Let’s look at an example —

```swift
struct School {
    var numberOfStudents = 0
    
    mutating func addSomeStudents(students: Int) {
        numberOfStudents += students
    }
}
```

#### Changing self

Mutating methods can assign a new instance to the self property. The same is not possible for classes which are reference types.

```swift
struct Aquarium {
    var numberOfFish: Int
    var someRandomFish = Int.random(in: 1...10)
    
    mutating func newNumberOfFish(fishes: Int) {
        self = Aquarium(numberOfFish: fishes)
    }
}
```

Here we define a structure `Aquarium` that has a property named `numberOfFish`. We have a mutating function that takes in `fishes` as its parameter and then goes on to set the `self` property to a new instance of `Aquarium` using the new number of fishes. Let’s try to verify if it actually creates a new instance or not —

```swift
var aquarium = Aquarium(numberOfFish: 10)
print(aquarium.someRandomFish)
aquarium.newNumberOfFish(fishes: 15)
print(aquarium.someRandomFish)
```

If you run this, most of the times you will observe that the `someRandomFish` property has changed even though there is no method which tries to change it. This is because on creating a new instance of the `Aquarium` struct, we end up generating a random number for our `someRandomFish` property.

### Type Methods

Just like properties can be **instance** and **type** properties, methods can be too. We have already looked at instance methods which are called from **instances** of our type. Now we will look at **type methods** which work quite similar to **type properties**.

#### Definition

```swift
struct TestStruct {
    static func typeMethod() {
        // Do something here
    }
}
```

Defining a **type method** inside a **struct** is exactly same as defining a **type property**. Let’s see an example of a type method inside a **class** —

```swift
class TestClass {
    static func typeMethod1() {
        // Do something here
    }
    
    class func typeMethod2() {
        // Do something here
    }
}
```

Now this is where we see a slight difference, we can use both static and class keywords to define type methods in a class. What is the difference? Class methods can be overridden by sub-classes.

#### Usage

Just like type properties, type methods can only be called upon from the type itself and not its instance. You can use them to modify type properties but not instance properties. You can also use them to generate an instance of your type. Let’s look at an examples —

```swift
class MoneyExchange {
    static var rateMultiplier = 50.0
    
    static func changeMultiplier(rateMultiplier: Double) {
        self.rateMultiplier = rateMultiplier
    }
    
    func calculateMoney(forAmount: Double) -> Double {
        return forAmount * MoneyExchange.rateMultiplier
    }
}
```

We have a class named `MoneyExchange` which has a type property named `rateMultiplier`, a type method named `changeMultiplier` and an instance method named `calculateMoney`. Let’s see this in action —

```swift
let exchanger = MoneyExchange()
print(exchanger.calculateMoney(forAmount: 10))
// Output - 500.0

MoneyExchange.changeMultiplier(rateMultiplier: 100)
print(exchanger.calculateMoney(forAmount: 10))
// Output - 1000.0
```

Let’s go line by line to see what is happening here —
- `Line 1` - We create an instance of our class `MoneyExchange`.
- `Line 2` - We call the instance method `calculateMoney` and supply 10 as an argument. The method then multiplies it with the type property `rateMultiplier` which is currently set to 50 and thus we get the output 500
- `Line 5` - We call the type method `changeMultiplier` to set the type property `rateMultiplier` to 100.
- `Line 6` - We again call the instance method `calculateMoney` and supply 10 as an argument. This time `rateMultiplier` is set to 100 and thus we get the output 1000.

This example alone should provide you a lot of insight as to how type properties and type methods work together.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.