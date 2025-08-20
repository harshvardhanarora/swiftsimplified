---
title: Basic Operations in Swift
date: 2022-03-01 00:00:00
categories: [Swift Basics]
tags: [swift]
---

### Terminology

**Unary Operators** — Operate on Single Target. Can be prefix (!a) or postfix (a!)  
**Binary Operators** — Operate on Two Targets. Can only be infix (a + b)  
**Ternary Operators** — Operate on Three Targets. Only one in Swift — (a ? b : c)  

### Assignment Operator

```swift
let a = 45
let b = a
b // 45

let (x, y) = (a, b)
(x, y) // x = 45 and y = 45
```
> Here, (x, y) is a compound type called **Tuple**.

### Arithmetic Operators

```swift
4 + 7 // 11
5 - 3 // 2
6 * 2 // 12
15 / 2 // 7
15.0 / 2 // 7.5
15 / 2.0 // 7.5
15.0 / 2.0 // 7.5
```

### Remainder Operator

```swift
9 % 4 // 1
-9 % 4 // -1
9 % -4 // 1
```

### Unary Minus Operator

```swift
let one = 1
let minusOne = -one // -1
```

### Unary Plus Operator

```swift
let two = -2
let tryPlusTwo = +two // -2
let plusTwo = -two // 2
```

### Compound Assignment Operators

```swift
var num = 5
num = num + 2 //7
num += 2 // 9
num *= 5 // 45
num /= 3 // 15
```

### Comparison Operators

```swift

let num1 = 12
let num2 = 15
num1 > num2 // false
num1 >= num2 // false
num1 == num2 // false
num1 <= num2 // true
num1 < num2 // true

/*
 Compare Strings
 Compares characters from left to right till first inequality if found which decides the result of the comparison
 Only equal if all characters are equal
*/

"Hello" > "Helmet" // false because Hello appears before Helmet in a dictionary
"Apple" > "Ant" // true because Ant appears before Apple in a dictionary
"Banana" == "Banana" // true because both strings are equal

"b" > "C" // Even though b appears before C in a dictionary, this statement is true
"b".uppercased() > "C".uppercased() // That's why it is better to compare strings after converting them both to uppercase or lowercase

/*
 Compare Tuples
 Can compare tuples if they have same type and same number of values
 Compares elements from left to right till first inequality is found which then decides the result of the comparison
 Only equal if all elements are equal
*/

let tuple1 = (5, "Train")
let tuple2 = (5, "Plane")
let tuple3 = (9, "Apple")
let tuple4 = (5, "Train")

tuple1 > tuple2 // true
tuple3 > tuple2 // true
tuple4 == tuple1 // true
```

### Ternary Conditional Operator

> question ? answerIfTrue : answerIfFalse  
> The type of answerIfTrue and answerIfFalse should be the same as the expected return type.

```swift
let passed: Bool
let marks = 74
passed = marks >= 50 ? true : false // true because marks >= 50
passed = marks >= 50 ? "Pass" : "Fail" // Error as we returned a String from the operator when a Boolean was expected
```

### Nil-Coalescing Operator

> a ?? b — Unwraps an optional a if it contains a value or returns b.  
> a should be an optional and b should not while matching the type of a.  
> Equivalent of a != nil ? a! ? b  

```swift
let optional1: Int? = nil
let optional2: Int? = 5
let notOptional = 12

let result1 = optional1 ?? notOptional // 12
let result2 = optional2 ?? notOptional // 5
// The type of result1 and result2 is now Int and not Int?
```

### Range Operators

> For a…b (Closed Range) and a..<b (Open Range), a <= b

```swift
// Closed Range Operator

let closeRange = 5...10
for index in closeRange {
    print(index, terminator: " ") // 5 6 7 8 9 10
}

print("") // Only to shift to next line in console

// Half Open Range Operator

let openRange = 5..<10
for index in openRange {
    print(index, terminator: " ") // 5 6 7 8 9
}

print("") // Only to shift to next line in console

// One Sided Range

let array = ["A", "B", "C", "D", "E"]
for alphabets in array[0...] {
    print(alphabets, terminator: " ") // A B C D E
}
```

### Logical Operators

```swift
// NOT

let trueValue = true
!trueValue // false

// AND

let anotherTrueValue = true
let falseValue = false
trueValue && anotherTrueValue // true
trueValue && falseValue // false

// OR

trueValue || anotherTrueValue // true
trueValue || falseValue // true

// Combining Logical Operators

trueValue && anotherTrueValue && falseValue // false
trueValue && anotherTrueValue || falseValue // true

// Explicit Paranthesis Usage

trueValue && !anotherTrueValue && falseValue // false
trueValue && !(anotherTrueValue && falseValue) // true
```