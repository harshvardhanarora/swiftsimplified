---
title: Closures in Swift
description: Hands-on guide to Swift closures - syntax, shorthand forms, trailing closures, capturing values, escaping and autoclosures with practical examples.
date: 2022-04-13 00:00:00
categories: [Swift Basics]
tags: [swift, closures]
---
Closures are a block of code that can be passed around as used like other properties in your Swift code. We will look at how to create and use different forms of closures and also understand how they capture values from their surrounding context.

### Function as a Parameter

Consider that you have an array of marks for your students —

`let marks = [45, 68, 75, 95, 25]`

And you have a function that returns the grades for these marks —

```swift
func gradingFunction(_ marks: Int) -> String? {
    switch marks {
        case 0..<30: return "F"
        case 30..<40: return "D"
        case 40..<50: return "C-"
        case 50..<60: return "C"
        case 60..<70: return "B-"
        case 70..<80: return "B"
        case 80..<90: return "A-"
        case 90...: return "A"
        default: return nil
    }
}
```

What we want to do now is apply this function to all the elements of the marks array. Now for simple cases such as this, you could simply traverse across the array and apply the function to each element and storing it in a new array. But Swift provides us with something more elegant which you will extensively use in your iOS Development journey — the `map` operator.

Let’s take a look at the map operator in Swift —

`@inline public func map<T>(_ transform: (Element) throws -> T) rethrows -> [T]`

Don’t be worried about what all those words in this method mean. The only thing you need to understand here is that the `map` method expects a function as its parameter which takes in an element and returns another element that is not necessarily the same as the input type. The `map` method itself returns an array of type same as the return type of the function that you passed in as its **argument**.

We will use map on this array and pass in our function which should return us an array of grades for the marks.

```swift
let grades = marks.map(gradingFunction)
// Output - ["C-", "B-", "B", "A", "F"]
```

The map function does not care about the name of the function or the name of the arguments for the function you pass in, it only cares about its **type**.

Try to autocomplete the map function, this is how it will look like —

```swift
let grades = marks.map { Int in
    
}
```

Everything after the map keyword is new to you, that is exactly how a `closure` looks like. So what you did in the above example is, pass in a **function as a closure argument**.

You could also move the code for the function inside this closure —

```swift
let newGrades = marks.map { (marks: Int) -> String? in
    switch marks {
        case 0..<30: return "F"
        case 30..<40: return "D"
        case 40..<50: return "C-"
        case 50..<60: return "C"
        case 60..<70: return "B-"
        case 70..<80: return "B"
        case 80..<90: return "A-"
        case 90...: return "A"
        default: return nil 
    }
}
```
Let’s understand what the above piece of code does —
- You assign the values returned from the right hand side of the expression to a property called `newGrade`
- You call the `map` method on `marks` array (map is available on all collection types) which takes in a closure as its argument
- You first specify the type of the closure which says that it takes in an argument called `mark` which is of type `Int` which then returns an **Optional String**
- The `in` keyword signifies the beginning of the closure body inside of which you are supposed to return a value which should be of the same type as the return type of the closure

---

### Shorthand Syntax for Closures

Let’s take a look at another example of a closure —

```swift
let sortedMarks = marks.sorted(by: { (m1: Int, m2: Int) -> Bool in
    return m1 < m2
})
```

`Sorted` is another method that you can call on any collection. It takes in a closure which takes in two parameters of type same as the type of the collection type you call the method `sort` on.

Now we have seen how Swift is intelligent in inferring the type of certain elements based on the context they are passed in.

#### Inferring Type from Context

Swift knows that the type of `m1` and `m2` will be `Int`. It also knows that the return argument from the closure provided as an argument will be a `Bool`. Therefore, we can remove those explicit types.

```swift
marks.sorted(by: { (m1, m2) in
    return m1 < m2
})
```

Looks a lot shorter and cleaner right? It’s important to be careful while doing this. Sometimes Swift cannot infer the return type of your closure in which case you need to be explicit about it.

We’re not done yet with shortening this closure!

#### Single Expression Closure

If the closure body has a single executable statement, you do not need to specify the return keyword. So let’s remove it.

```swift
marks.sorted(by: { (m1, m2) in
    m1 < m2
})
```

We’re still not done…

##### Shorthand Argument Names

Swift allows us to omit the argument list from the closure definition by making use of shorthand argument names — $0, $1, $2 etc.

Let’s see how it works —

`marks.sorted(by: { $0 < $1 })`

Although we’ve come a long way in shortening this from what we started with, we have one more concept to look at — **Trailing Closures**.

#### Trailing Closures 

If you are passing a closure as an argument inside a function, and it is the last argument for the function, you can write it as a trailing closure. Let’s see it in action —

```swift
marks.sorted(by: { $0 < $1 })

marks.sorted() { $0 < $1 }
```

**Sorted** method takes in a single closure as its argument. In the first line, we write it as we have done till now, but in the second line we change a few things.
1. We remove the argument name for the closure
2. The closure code inside {} is no longer a part of () and is now written after
   
Since this method does not take any argument, you can also remote the ()

`marks.sorted { $0 < $1 }`

Let’s look at another example where we will create out own method that takes in multiple closures as its arguments, and try to shorten it step by step.

```swift
func compute(num: Int, ifEven: (Int) -> String, ifOdd: (Int) -> String) {
    if num % 2 == 0 { ifEven(num) }
    else { ifOdd(num) }
}
```

This method takes in a number as its input argument, along with two functions as parameters. If the number is even, we call the first function and if it is odd we call the second one.

Let’s see how we would call this method using closures —

```swift
compute(
    num: 5,
    ifEven: { (num: Int) -> String in
        return "\(num) number is even"
    },
    ifOdd: { (num: Int) -> String in
        return "\(num) number is odd"
    }
)
```

This is how we would call this function with closures if we did not know about **short hand syntax**. Hopefully at this point you understand everything that’s happening in here. Let’s try to shorten it shall we?

We know the functions are supposed to take in an integer and return a string. We also have a single executable statement, so we can remove the return keyword.

```swift
compute(
    num: 5,
    ifEven: { (num: Int) -> String in
        "\(num) number is even"
    },
    ifOdd: { (num: Int) -> String in
        "\(num) number is odd"
    }
)
```

Already looks so clean! Let’s also use shorthand argument names here —

```swift
compute(
    num: 5,
    ifEven: { "\(num) number is even" },
    ifOdd: { "\(num) number is odd" }
)
```

Looks amazing doesn’t it? One final thing, let’s see how trailing closures look in action when we have multiple trailing closure arguments —

```swift
compute(num: 5) { "\(num) number is even" }
    ifOdd: { "\(num) number is odd" }
```

In case of multiple closure arguments, we write the first one as we would normally write a trailing closure. But for the second closure, we would first write the argument label and then write the closure body itself as seen above.

---

### Capturing Values

A closure can capture constants and variables from the surrounding context in which it is defines. Let’s try and take a look at what this means.

```swift
func gradeListCompute() -> (Int) -> [String] {
    var grades: [String] = []

    func computeGrade(marks: Int) -> [String] {
        if let grade = gradingFunction(marks) {
            grades.append(grade)
        }
        return grades
    }

    return computeGrade
}
```

Let’s try to understand what we have here —

1. A function **gradeListCompute** that takes in no arguments and returns a function which itself takes in an integer and return an array of strings.
2. Inside the function, we create an array of strings called **grades** which will store the grades of students.
3. We create a function **computeGrade** inside our parent function that takes in an integer as input and returns an array of strings.
4. Inside the **computeGrade** function, we call the **gradingFunction** method that we created in the beginning of this article to compute the grade for the marks scored. If you remember correctly, gradingFunction returns an **optional** due to which we have used **if-let** syntax to safely unwrap it.
5. We append the grade to the **grades** array that we created in our parent function. Now finally the **gradeListCompute** function returns the **computeGrade** function as its output.
   
You must have noticed by now that the **computeGrade** method appends the **grades** property that was created in the parent function.

Let’s see how this function would look like in action —

```swift
var finalGrades: [String] = []
let gradeList = gradeListCompure()

for mark in marks {
    finalGrades = gradeList(mark)
    print(finalGrades)
}
```

The output for this will be -

```
["C-"]
["C-", "B-"]
["C-", "B-", "B"]
["C-", "B-", "B", "A"]
["C-", "B-", "B", "A", "F"]
```

It looks pretty simple. We iterate over the array of `marks` we created at the beginning, and run the individual mark through the `gradeList` function. Nothing special here right?

So where did the concept of capturing values come?

Variables and constants are not present in the memory forever. They are automatically removed from memory by Swift when the **scope** they were created in is destroyed. For functions, that happens when the function is finished executing.

So in our `gradeListCompute` method, the `grades` array should have been removed from memory after the function finished executing. But as you can see from the output, that does not happen. Every time we call the `gradeList` method, we append a value to the `grades` array and return it.

This happens because the function `computeGrade` captures the `grades` array. What this means is, it holds a reference pointer to the `grades` array which allows it to remain in memory even when the scope in which it was created (the `gradeListCompute` function) no longer exists.

Capturing can sometimes be a two-way sword which when used wrongly can create a lot of problems in your code. We will talk more on that in later articles, but for now this was a great introduction to this concept!

> Closures are reference types. If you create a new property and assign it to `gradeList` and then call it with an integer, it would append the `grade` to the existing grade list.

```swift
let newGradeList = gradeList

["C-", "B-", "B", "A", "F", "A"]
```

---

### Escaping Closures

A closure is said to be escaping a function when it is passed in as an argument to that function but is executed after the function has returned. This usually happens when you make an async call to perform some operation and then call the closure method once that call completes. It can also happen if you store the closure inside a property defined outside the closure. Let’s look at a simple example to understand this —

```swift
var closures: [() -> Void] = []

func addClosure(with closure: @escaping () -> Void) {
    closures.append(closure)
}
```

There are some things to be careful about here in terms of not creating a strong memory cycle, but we will cover that in detail in subsequent articles.

---

### Auto Closure

Auto closure automatically converts the argument being passed to a function into a closure. Let’s look at an example of writing a simple closure —

```swift
func performClosure(closure: () -> Void) {
    closure()
}

performClosure(closure: { print("I am a closure") })
```

Here we pass in a closure to a function which then executes that closure. Simple right? Notice how we write the closure argument inside `{}`.

Let’s see how we could do this with an auto closure —

```swift
func performAutuClosure(closure: @autoclosure () -> Void) {
    closure()
}

performAutoClosure(closure: print("I am a closure"))
```

Here the argument is automatically converted to a closure, so you do not need to surround it with {} anymore.
You should not overuse auto closures as it reduces the readability of the code. You can also have closures that are both **escaping** and **autoclosures**.

That’s it! That was quite a journey we covered with a lot of important concepts. Hopefully you now have a good working understanding of how closures work in Swift. Feel free to reach out to me in case you have any questions. Do follow my page to receive more articles on Swift fundamentals.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.