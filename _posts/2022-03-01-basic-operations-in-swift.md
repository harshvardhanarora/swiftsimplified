---
title: Basic Operations in Swift
date: 2022-03-01 00:00:00
categories: [Swift Basics]
tags: [swift]
---
### Terminology

- Unary operator — operates on a single value. Examples: prefix (-a) and postfix (a!).
- Binary operator — operates on two values, usually written in infix position (a + b).
- Ternary operator — combines three operands; Swift has one: the conditional operator `a ? b : c`.

### Assignment

Assignment stores a value into a variable or constant. Tuples support destructuring assignment.

```swift
let a = 45
let b = a // b is 45

let (x, y) = (a, b)
// x == 45, y == 45
```

`(x, y)` above is a tuple — a lightweight compound value that groups multiple values.

### Arithmetic operators

Basic arithmetic works as expected. Integer division discards the fractional part; use floating-point types for fractional results.

```swift
4 + 7      // 11
5 - 3      // 2
6 * 2      // 12
15 / 2     // 7   (integer division)
15.0 / 2   // 7.5 (floating-point division)
```

### Remainder operator

The remainder (%) keeps the sign of the left-hand operand in Swift.

```swift
9 % 4    // 1
-9 % 4   // -1
9 % -4   // 1
```

### Unary plus & minus

Use unary minus to negate a number. Unary plus is allowed but usually has no effect.

```swift
let one = 1
let minusOne = -one   // -1

let two = -2
let plusTwo = -two    // 2
let tryPlusTwo = +two // still -2
```

### Compound assignment

Short-hand assignment operators combine an operation with assignment.

```swift
var num = 5
num = num + 2 // 7
num += 2      // 9
num *= 5      // 45
num /= 3      // 15
```

### Comparison operators

Standard comparisons return `Bool`.

```swift
let num1 = 12
let num2 = 15
num1 > num2   // false
num1 >= num2  // false
num1 == num2  // false
num1 <= num2  // true
num1 < num2   // true
```

Strings are compared lexicographically (left-to-right) and comparisons are case-sensitive by default. To perform case-insensitive comparisons, normalize both sides first:

```swift
"b" > "C"                           // true (case-sensitive)
"b".uppercased() > "C".uppercased() // false (compare same case)
"Apple" > "Ant"                     // true (p > n)
"Hello" > "Helmet"                  // false (m > l)
```

Tuples of the same type and arity can be compared element-wise from left to right.

```swift
let tuple1 = (5, "Train")
let tuple2 = (5, "Plane")
let tuple3 = (9, "Apple")
let tuple4 = (5, "Train")

tuple1 > tuple2  // true ("Train" > "Plane")
tuple3 > tuple2  // true (9 > 5)
tuple4 == tuple1 // true
```

### Ternary conditional operator

The ternary operator is a compact form of an `if` that returns one of two values.

```swift
let marks = 74
let passed = marks >= 50 ? true : false
// prefer the simpler form when returning Bools
let passedSimple = marks >= 50
```

Avoid returning values of different types from the two branches; they must share the same type.

### Nil-coalescing operator

`a ?? b` unwraps optional `a` if it has a value, otherwise returns `b`. The result is non-optional.

```swift
let optional1: Int? = nil
let optional2: Int? = 5
let notOptional = 12

let result1 = optional1 ?? notOptional // 12
let result2 = optional2 ?? notOptional // 5
// result1 and result2 are Int (not Int?)
```

### Range operators

Swift has closed (`a...b`), half-open (`a..<b`) and one-sided ranges (`a...` or `...b`).

```swift
let closed = 5...10
for i in closed { print(i, terminator: " ") } // 5 6 7 8 9 10

let halfOpen = 5..<10
for i in halfOpen { print(i, terminator: " ") } // 5 6 7 8 9

let array = ["A", "B", "C", "D", "E"]
for letter in array[0...] { print(letter, terminator: " ") } // A B C D E
```

### Logical operators

Logical operators work on Booleans: NOT (`!`), AND (`&&`) and OR (`||`). Use parentheses to make complex expressions explicit.

```swift
let trueValue = true
let falseValue = false

!trueValue               // false
trueValue && true        // true
trueValue || falseValue  // true

// Combined with parentheses for clarity
trueValue && !(falseValue || false)
```

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.