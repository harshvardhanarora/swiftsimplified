---
title: Control Flow in Swift
description: 
date: 2022-03-22 00:00:00
categories: [Swift Basics]
tags: [swift]
---
Switch statements in Swift are very powerful and will be covered extensively in this tutorial. We will also look at other control flow statements like for-in, while, if, guard, break and continue.

### For-In

We’ve already seen this statement being used to iterate over collections in our tutorial [Collection Types in Swift](../collection-types-in-swift/). Let’s go over them once again!

#### Iterating over an Array

```swift
let names = ["Max", "Charles", "Lewis", "Lando", "George"]
for name in names {
    print(name, terminator: " ")
}
```

#### Iterating over a Dictionary 

```swift
let nameForNumber = [1: "Max", 16: "Charles", 44: "Lewos", 4: "Lando", 63: "George"]
for (number, name) in nameForNumber {
    print("Number for \(name) is \(number)")
}
```

We can also chose to iterate over only keys or values of a dictionary as well.

```swift
for number in nameForNumber.keys {
    print(number, terminator: " ")
}
for name in nameForNumber.values {
    print(name, terminator: " ")
}
```

### While

While loops is used to perform a set of statements until a stopping condition is reached.

```swift
var startNumber = 10
let maxNumber = 45
while startNumber <= maxNumber {
    startNumber += 6
}
print(startNumber) // Output - 46
```

What this loop does is, that it keeps on adding the integer `6` to the `startNumber` till it is lower than `maxNumber`. Important thing to note here is that the final value of `startNumber` is `46` which is greater than the `maxNumber`. This happens because in the last iteration of our loop, the value of `startNumber` would have been `40` which was still lower than `maxNumber` because of which the statement inside the while loop was executed.

If we wanted it to stop at `40`, we would need to use the break which we will look at soon.

### Repeat-While

This is very similar to the while loop, the only difference being that the first loop iteration is executed without checking any condition.

```swift
repeat {
    startNumber += 6
} while startNumber <= maxNumber
```

This control flow statement should be used when you need to execute the first iteration of the loop without checking the stopping condition.

### If-Else

We have used this statement extensively throughout our other tutorials as well so you should be familiar with its usage by now. We will still go over a couple examples just to recap your memory!

```swift
let truth = true
if truth {
    print("This is true")
}
```

We can also chain if-else statments —

```swift
let pointsA = 55
let pointsB = 57
if pointsA > pointsB {
    print("A won")
} else if pointsA == pointsB {
    print("A equal B")
} else {
    print("B won") // Output
}
```

### Switch

We’re finally here! Switch statements in Swift are very powerful and can be a bit difficult to get a grasp on at times, so I’d need you to be clear your head now before diving into this section. We’ll learn all about switch statements using mostly code with just a little bit of theory if needed.

We use switch statements when we want to compare a value against multiple possible values and then execute a block of statements when we find the matching pair. Let’s dive into some code!

```swift
let number = 5
switch number {
    case 1: print("This is number one")
    case 2: print("This is number two")
    case 3: print("This is number three")
    case 4: print("This is number four")
    case 5: print("This is number five") // Output
    default: print("This number is greater than five")
}
```

Although you’d never have to write something exactly like this code, but it is a great example to understand the basics of a switch statement.

> If you’ve used other programming languages, you must have noticed the absence of a `break` statement inside the `case` statements. In Swift, as soon as a matching case is found and executed, we break out of the `switch` statement automatically.

Every matching case needs to have at-least **one executable statement** or it would result in an error.

We can also club multiple cases into a single case —

```swift
switch number {
    case 1, 2, 3, 4, 5: print("This number is between one and five")
    default: print("This number is greater than five")
}
```

Switch statements are exhaustive in nature. The cases should include all possible values that the type of your value that you compare for. In this example, `number` is of type `Int` and therefore the switch statement must cover all possible `Int` values. That is practically not possible for a lot of cases therefore we make use of `default` case. This case is executed if the switch statement is unable to match your value against all other cases.

We can also check for the inclusion of the value inside an interval —

```swift
switch number {
    case 1...5: print("This number is between one and five")
    default: print("This number is greater than five")
}
```

#### Tuples

Till this point we have been testing against a single value. But we can also test against tuples as well. (Told you switch was powerful in Swift!)

```swift
let graphPoint = (5, 0)
switch graphPoint {
    case (0, 0): print("Point is on the origin")
    case (_, 0): print("Point is on the X-Axis") // Output
    case (0, _): print("Point is on the Y-Axis")
    default: print("Point is not on the axis")
}
```
We use `_` to denote that we know there is a value at that point, but we do not care about it so just ignore it.

We can also chose to access those values instead of ignoring them . This is known as **value binding**. Example —


```swift
switch graphPoint {
    case (0, 0): print("Point is on the origin")
    case (let x, 0): print("Point is on the X-Axis with value \(x)") 
    case (0, let y): print("Point is on the Y-Axis with value \(y)")
    default: print("Point is not on the axis")
}
// Output - Point is on the X-Axis with value 5
```

We can also add conditions while matching cases using where statement to check for cases that match the required conditions —

```swift
switch graphPoint {
    case let (x, y) where x == 0 || y == 0: print("Point is on atleast one of the axis") // Ouput
    case let (x, y): print("Point is on x:\(x), y: \(y)") // This is not printed
}

switch graphPoint {
    case (0, 0): print("Point is on the origin")
    case (let distance, 0), (0, let distance): print("Point is at a distance of \(distance) from one of the axis") // Ouput
    default: print("Point is not on the axis")
}
```

In the second example, we also see that we can add multiple matching cases inside a single case for tuples as well. The only thing to look out for is that all those tuples should use the same binding `name` and `type`. In the above example, `distance` is used in both tuples to bind for an `Integer`.

That concludes our introduction to Switch statements. Now we will dive into some statements that help us transfer control from one point in code to another.

### Continue

Continue statements stops the current iteration of a loop and tells the loop to start the next iteration.

```swift
for number in 1...5 {
    if number == 3 { continue }
    print(number, terminator: " ") // Ouput - 1 2 4 5
}
```

### Break

Break statements stops the entire execution of the loop immediately.

```swift
for number in 1...5 {
    if number == 3 { break }
    print(number, terminator: " ") // Ouput - 1 2
}
```

As you can see the difference, the first example only ignores the loop where the value of number was 3 and continue with rest of the loop iterations. In the second example, one the value of number was 3, we completely stopped the execution of the statement.

We can also break out of switch statements —

```swift
for iteration in 1...5 {
    switch iteration {
        case 1: print("1", terminator: " ")
        case 2: print("2", terminator: " ")
        case 3: break
        case 4: print("4", terminator: " ")
        case 5: print("5", terminator: " ")
        default: print("Default", terminator: " ")
    }
}
// Output - 1 2 4 5
```

### Fallthrough

As mentioned before, switch statements automatically break once a matching case is found. We can explicitly specify to fall onto the next statement using the `fallthrough` keyword.

```swift
for iteration in 1...5 {
    switch iteration {
        case 1: print("1", terminator: " ")
        case 2: print("2", terminator: " ")
        case 3: fallthrough
        case 4: print("4", terminator: " ")
        case 5: print("5", terminator: " ")
        default: print("Default", terminator: " ")
    }
}
// Output - 1 2 4 4 5
```

Important thing to note here is that the `fallthrough` would lead to the execution of the next case even if it does not match which can be noticed from the `4` being printed twice in the output.

### A complex scenario...

We will look at an example to look at how different control flow statements work together —

```swift
var totalSum = 0

sumLoop: while totalSum < 100 {
    let roll = Int.random(in: 1...10)
    switch roll {
        case let x where x % 2 == 0: totalSum += x * x
        case 1, 3, 5, 9: break // Breaks out of switch
        case 7: continue sumLoop // Moves on the the next sumLoop iteration
        case 10: break sumLoop // Stops both switch and sumLoop
        default: break // Breaks out of switch
    }
}
```

Now we will go line by line to understand what the above code does —
1 — Create a variable called totalSum
3 — Create a while loop named sumLoop which would execute till totalSum is less than 100
4 — Local constant roll which is a random number between 1 and 10
5 — Switch statement to check value of constant roll
6 — If roll is divisible by 2, adds its squared value to totalSum
7 — Breaks the switch statement if value of roll is 1, 3, 5, 9
8 — Moves on to next iteration of sumLoop if value of roll is 7
9 — Stops both the switch statement and the sumLoop if value is 10

This might take a bit of time to digest, and that is okay. Try this out in your playground and keep trying to change thins here and there to understand it better.

### Guard

Now lastly, we will cover the guard statement. It is very similar to an if statement the only difference being we use guard when the condition must be true for the code to move forward.

```swift
let driverNumForName = ["Max": 1, "Charles": 16, "Lewos": 44, "Lando": 4, "George": 63]

func findDriverNumber(for name: String) {
    guard let number = driverNumForName[name] else {
        print("Driver not found")
        return
    }
    print(number)
}

findDriverNumber(for: "Ocon")

// Output - Driver not found
```

---

That’s it! That was a lot of ground to cover and we did it! It will take some time to get your head around these concepts at the beginning, but its important to practice and be patient at the same time. Feel free to reach out to me in case you have any queries!

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.