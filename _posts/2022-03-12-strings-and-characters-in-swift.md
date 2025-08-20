---
title: Strings and Characters in Swift
description: Master string and character handling in Swift with this practical guide covering literals, mutability, interpolation, Unicode, grapheme clusters, indexing, modification, substrings, and comparison techniques.
date: 2022-03-12 00:00:00
categories: [Swift Basics]
tags: [swift, strings]
---

Strings are series of characters represented by the String type in Swift. This article will cover different ways to play around with Strings and Characters in Swift.

### String Literals

```swift
let stringLiteral = "This is a string literal"
// Type of stringLiteral is inferred as String

let multiLineLiteral = """
    We can also have multi-line string literals.
    This line break will also appear in string's value.
    We can also chose to not have a line break using \
    which allows us to organise a string in a better way.
        The space behind the closing marks tells Swift to ignore that for all lines.
    """
```
### Initialising empty String

```swift
let emptyStringLiteral = "" // Output - ""
let emptyString = String()  // Output - ""
```

### String Mutability

```swift
var firstName = "Max"
let lastName = "Verstappen"
firstName += " " + lastName
// Output - Max Verstappen

lastName = firstName + lastName
// Throws an error because lastName is a constant

let multiLineString = """
    A
    B
    """
// Include a line space in multi-line strings to create a line break
let characterC: Character = "C"
// Character does not have string literal
print(multiLineString + String(characterC))
/*
Output -
A
B
C
*/
```

The important thing to note here is that inside multi-line strings, we need to manually insert a blank line to create a line break. Without that the output would have looked like -

```
A
BC
```

### Iterating over Strings

```swift
let driver = "Lewis Hamilton"
for character in driver {
print(character, terminator: " ")
// Output - L e w i s  H a m i l t o n
}
```

By default, each print statement includes a line break at the end. By using terminator argument, we can specify our own custom end string for each print statement. Here we chose to insert a blank space after each print statement.

### String Interpolation

```swift
let points = 395.5
let statement = "Max Verstappen accumulated \(points) points in the F1 2021 season"
```

### Unicode and Extended Grapheme Clusters

Now even though I try to keep my tutorials mostly code, sometimes a little theory can be crucial to understand some key concepts. So bear with me for just a minute!

> Every **Character** instance represents a single **extended grapheme cluster**.

Every extended grapheme cluster is made up of one or more unicode scalar values to form a human-readable character. An example of this is, the letter é can be written through a single unicode scalar or a combination of two unicode scalars.

```swift
let acuteE: Character = "\u{E9}"          // Output - é
let combineE: Character = "\u{65}\u{301}" // Output - é
```
The **count** of a string is calculated by the number of extended grapheme clusters it is composed of and not unicode scalars. What this implies is that when we concatenate strings, the count of the final string will **not always** be equal to the sum of counts of member strings.

```swift
var cafe = "cafe"
print(cafe) // Output - 4
cafe += "\u{301}" // Output - café
print(cafe) // Output - 4
```

As we see here, going from cafe to café the count of characters remained 4.
Alright that’s enough theory, hope you got the gist of it. Let’s move on!

### Accessing a String

```swift
let helloWorld = "Hello World!"
var startIndex = helloWorld.startIndex
print(helloWorld[startIndex])
// Output - H

var endIndex = helloWorld.endIndex
// print(helloWorld[endIndex])
// Throws an error because the endIndex is after the last character

endIndex = helloWorld.index(before: endIndex)
print(helloWorld[endIndex])
// Output - !

let fifthIndex = helloWorld.index(startIndex, offsetBy: 4)
print(helloWorld[fifthIndex])
// Output - o

for index in helloWorld.indices {
    print(helloWorld[index], terminator: "")
}
```

### Modifying a String

```swift
var hello = "Hello"

hello.insert("!", at: hello.endIndex)
print(hello)
// Output - Hello!

hello.insert(contentsOf: " World", at: hello.index(hello.startIndex, offsetBy: 5))
print(hello)
// Output - Hello World!

hello.remove(at: hello.index(before: hello.endIndex))
print(hello)
// Output - Hello World

let range = hello.startIndex...hello.index(hello.startIndex, offsetBy: 5)
hello.removeSubrange(range)
print(hello)
// Output - World
```

### Substring

```swift
let helloWorldAgain = "Hello, World!"
let helloIndex = helloWorldAgain.firstIndex(of: ",") ?? helloWorldAgain.endIndex
let helloSubstring = helloWorldAgain[..<helloIndex]
// Output - Hello
```

When you create a substring like above, it is of type String.Subsequence. As a performance optimisation, a substring can reuse part of memory that’s used to store the original string (until the string or substring is changed). That’s why it is recommended to use substring only for a shorter scope. Otherwise, you can create a separate string from it —

`let helloString = String(helloSubstring)`

### Comparing Strings

An extended grapheme cluster can be formed using different combinations of unicode scalars. Two strings are considered equal if their extended grapheme clusters are **canonically equivalent** (same linguistic meaning and appearance). Therefore, café written through the below combinations will be the same,

```swift
let acuteLatte = "Latt\u{E9}"
let combinedLatte = "Latt\u{65}\u{301}"
print(acuteLatte == combinedLatte)
// Output - True
```

but the alphabet a in Latin and Russian will be differnt.

```swift
let latinCapitalLetterA: Character = "\u{41}"
let cyrillicCapitalLetterA: Character = "\u{0410}"
print(latinCapitalLetterA == cyrillicCapitalLetterA)
// Output - False
```

We can also compare using `hasPrefix(_:)` and `hasSuffix(_:)`.

```swift
let ferrariDriver = "Charles Leclerc"
print(ferrariDriver.hasPrefix("Charles"))
// Output - True
print(ferrariDriver.hasSuffix(" Leclerc"))
// Output - True
```

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.