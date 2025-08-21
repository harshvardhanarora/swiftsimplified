---
title: Strings and Characters in Swift
description: Practical guide to Swift strings and characters - literals, mutability, interpolation, Unicode and grapheme clusters, indexing, substrings, and comparison techniques with copy-friendly examples.
date: 2022-03-12 00:00:00
categories: [Swift Basics]
tags: [swift, strings]
---
Strings are sequences of user-perceived characters represented by the `String` type in Swift. This article explains string and character basics with small, copy-friendly examples.

### String literals

Single-line string literals are written with double quotes. Use triple quotes for multi-line literals. Indentation rules are available to trim leading whitespace in multi-line strings.

```swift
let stringLiteral = "This is a string literal"

let multiLineLiteral = """
We can write multi-line strings.
Line breaks inside the triple quotes are preserved.
"""
```

### Empty strings

You can create an empty string using a literal or the `String` initializer:

```swift
let emptyLiteral = ""
let emptyString = String()
```

### Mutability

Use `var` for strings you plan to modify and `let` for constants.

```swift
var firstName = "Max"
let lastName = "Verstappen"
firstName += " " + lastName
```

The example above appends the last name to `firstName`. Attempting the same assignment to a `let` constant would cause a compile-time error.

Multiline string example and combining with a `Character`:

```swift
let multiLine = """
A
B
"""
let char: Character = "C"
let combined = multiLine + String(char)
```

The `combined` string now contains the three lines with `C` appended on a new line.

### Iterating over characters

Iterate a string to access its `Character` values:

```swift
for ch in "Swift" {
    print(ch)
}
```

Each loop iteration yields a `Character` (a user-perceived character), which may consist of one or more Unicode scalars.

### String interpolation

Insert values into strings using `\( )`:

```swift
let points = 395.5
let statement = "Max Verstappen accumulated \(points) points"
```

### Unicode and extended grapheme clusters

Each `Character` represents an extended grapheme cluster — one or more Unicode scalars that form a single user-perceived character. For example, "é" can be a single scalar (`\u{E9}`) or `e` plus a combining accent (`\u{65}\u{301}`); they are considered canonically equivalent.

```swift
let acuteE: Character = "\u{E9}"
let combinedE: Character = "\u{65}\u{301}"
```

The two `Character` values above compare as equal because they represent the same grapheme cluster.

Note: `String.count` counts extended grapheme clusters (user-perceived characters), not Unicode scalars or bytes.

```swift
var cafe = "cafe"
cafe.count
cafe += "\u{301}"
cafe.count
```

Here, `cafe.count` remains 4 after appending the combining accent because the result is still four user-perceived characters: `c`, `a`, `f`, `é`.

### Accessing characters and indexes

Swift strings use `String.Index` instead of integer offsets because of variable-width Unicode characters.

```swift
let s = "Hello World!"
let start = s.startIndex
let first = s[start]
let fifth = s[s.index(start, offsetBy: 4)]
```

In the example above `first` is "H" and `fifth` is "o". Note that `s.endIndex` represents the position after the last character and cannot be subscripted directly.

To iterate by index:

```swift
for idx in s.indices {
    print(s[idx], terminator: "")
}
```

### Modifying strings

You can insert, append, and remove characters or ranges using the `String` API.

```swift
var t = "Hello"
t.insert("!", at: t.endIndex)
t.insert(contentsOf: " World", at: t.index(t.startIndex, offsetBy: 5))
t.remove(at: t.index(before: t.endIndex))
```

After these operations `t` becomes first "Hello!", then "Hello World!", and finally "Hello World" after removing the trailing exclamation mark.

### Substrings

Ranges of a `String` produce `Substring` (a `String.SubSequence`) which can share storage with the original string. Convert to `String` when you need an independent copy.

```swift
let hw = "Hello, World!"
let idx = hw.firstIndex(of: ",") ?? hw.endIndex
let sub = hw[..<idx]
let helloString = String(sub)
```

`sub` is a `Substring` view into `hw` containing "Hello"; `helloString` converts it to an owned `String`.

### Comparing strings

String equality compares canonical equivalence of grapheme clusters.

```swift
let a = "Latt\u{E9}"
let b = "Latt\u{65}\u{301}"
```

The two strings above are considered equal because they represent the same user-perceived characters.

Be aware that characters from different scripts (Latin vs Cyrillic) are distinct even if they look similar:

```swift
let latinA: Character = "\u{41}"
let cyrillicA: Character = "\u{0410}"
```

`latinA == cyrillicA` is `false` — the code points are different.

Use `hasPrefix(_:)` and `hasSuffix(_:)` for common prefix/suffix checks.

```swift
let name = "Charles Leclerc"
name.hasPrefix("Charles")
name.hasSuffix("Leclerc")
```

These return `true` for the example above.

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.