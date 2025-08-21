---
title: Subscripts in Swift
description: How to implement subscripts in Swift types - read-only and read-write subscripts, multiple parameters, overloading, and type subscripts with examples.
date: 2022-06-17 00:00:00
categories: [Swift Basics]
tags: [swift, subscripts]
---
How do you access an element of an array?

`let element = array[5]`

The above property `element` is the **sixth** element from the array. But what does the `[5]` mean? We’ve also seen this on dictionaries where we access a value using a key. Is this syntax something we can define for our own types? That’s what we’ll explore in this article.

---

#### How to create a Subscript?
Let’s dive straight into an example as to understand subscripts —

```swift
struct Stadium {
    let numberOfLegsPerPerson = 2
    
    subscript(numberOfPeople: Int) -> Int {
        numberOfLegsPerPerson * numberOfPeople
    }
}
```

We define a struct `Stadium` that has a stored constant property named `numberOfLegsPerPerson`. Line 10 is where we define our subscript. This is an example of a **read-only** subscript. Let’s see it in action —

```swift
let stadium = Stadium()
print(stadium[5])
// Output - 10
```

We create an instance of our struct, and then use the [] syntax on that instance along with an integer passed inside those brackets. Swift is able to recognise that you have a `subscript` that takes in an integer and thus calls the subscript we defined inside our structure.

As I mentioned above, this was an example of a **read-only** subscript. Creating a **read-write** subscript is similar to a computed property. Let’s look at the syntax —

```swift
struct SomeStruct {
    subscript(someInput: String) -> Int {
        get {
            return someInput.count
        }
        set(someNewValue) {
            // Do something here
        }
    }
}
```

As simple as that. You can remove the `someNewValue` yet the property would be available under the name `newValue` which can be accessed inside the set closure.

#### Multiple parameters in Subscript

The above examples had a single parameter in the subscript. You can have multiple parameters as well. Let’s look at an example —

```swift
struct Area {
    subscript(length: Double, width: Double) -> Double {
        return length * width
    }
}

let area = Area()
print(area[4.5, 2])
// Output - 9.0
```

You can have more than 2 parameters as well. There is no limit there.

#### Overloading Subscript

We can also have multiple subscript inside a type. Which one gets called depends on the input parameters and their type while calling that subscript. You can have overloading subscripts if the **parameter type** of parameters is different or the **number of parameters** is different. You can also overload subscript with different **return** type but that can only work under certain circumstances. Let’s take a look at a few examples —

```swift
struct Overloading {
    subscript(someInteger: Int) -> String {
        return "Hello"
    }
    subscript(anotherInteger: String) -> String {
        return "Hello again"
    }
}
```

The two subscripts have different parameter types thus the output will depend on that —

```swift
let overloading = Overloading()
print(overloading[4])
// Output - Hello
print(overloading["4"])
// Output - Hello again
```

It will work similarly for different number of parameters. Let’s look at the case when return type is different —

```swift
subscript(someString: String) -> Int {
    return 5
}
```

Add the above subscript to your code and do nothing else. You will see that the print(overloading[“4”]) is now giving your an error and the playground does not run. What happened here?

If you go through the code carefully, you will understand that now there are two subscripts that take String as a parameter but they return Int and String. When you try to print the output of that subscript, Swift does not know which subscript you are referring to. Thus it throws the ambiguous use error —

> Ambiguous use of 'subscript(_:)'
{: .prompt-danger}

We can resolve this by explicitly specifying what we are expecting as a result from the subscript —

```swift
let stringOutput: String = overloading["4"]
print(stringOutput)
// Output - Hello again
let intOutput: Int = overloading["4"]
print(intOutput)
// Output - 5
```

Now Swift knows whether you are looking for the subscript which returns an `Int` or a `String`.

#### Type Subscript

Just like methods and properties, we have both instance and type subscripts. All the above examples you looked at were instance subscripts. Let’s take a look at a type subscript —

```swift
struct Week {
    enum Day: Int {
        case mon = 1, tue, wed, thu, fri, sat, sun
    }
    static subscript(dayOfWeek: Int) -> Day {
        Day(rawValue: dayOfWeek)!
    }
}
```

Note the force unwrap “!” at line 85, which would crash the program if dayOfWeek was outside the range 1…7! Let’s see this code in action —

> `print(Week[8])` - Crash
{: .prompt-danger}

Note that we call the subscript syntax on the type itself now instead of creating an instance of it and then calling it on that instance.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.