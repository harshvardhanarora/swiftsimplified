---
title: Collection Types in Swift
description: Explore Swift’s primary collection types—arrays, sets, and dictionaries—with practical, copy-friendly examples for creation, access, modification, iteration and set operations.
date: 2022-03-16 00:00:00
categories: [Swift Basics]
tags: [swift, collection]
---
Swift has three primary collection types — Arrays, Sets and Dictionaries. They provide different ways to store multiple values of the same type. This article focuses on hands-on examples for creating, accessing and modifying these collections with minimal theory.

---

### Arrays
Ordered collection of values of the same type.

#### Creating Arrays

```swift
var array0 = Array<Int>() // Empty Array
var array1: Array<Int> = [1]
var array2: [Int] = [2]
var array3 = [3] // Array Literal
```

As seen above, `array3` is created via an array literal. Swift automatically infers the type of the array to be of `Int`. You can now check the type of all the above arrays and see that they all will be the same.

```swift
for arr in [array0, array1, array2, array3] {
    print(type(of: arr)) // All print Array<Int>
}
```

You can also create an array which repeats a single number —

```swift
var array4 = Array(repeating: 5.3, count: 3)
print(array4) // Output - [5.3, 5.3, 5.3]
print(type(of: array4)) // Output - Array<Double>
```

As you can see, the data type of the array is inferred to be `Double` because you provided a decimal number inside the `init(repeating:count:)` method.

You can also combine two arrays —

```swift
var array5 = array1 + array2
print(array5) // Output - [1, 2]
```

#### Accessing and Modifying Arrays
You can check the number of items in an array or whether it is empty or not.

```swift
var shoppingList = ["Milk", "Eggs", "Paint", "Fruits"]
print(shoppingList.count) // Output - 4
print(shoppingList.isEmpty) // Output - false
```

You can add items to an array.

```swift
shoppingList.append("Vegetables")
shoppingList.append(contentsOf: ["Oil", "Shampoo", "Scrub"])
shoppingList += ["Pancake Mix"]
print(shoppingList.count) // Output - 9
```

Be careful when using the `+=` operator: the right-hand side must be an array with the same element type as the array you're adding to (for this case, `[String]`).

You can access and modify an element of the array using subscript syntax.

```swift
print(shoppingList[5]) // Output - Oil
shoppingList[5] = "Hair Oil"
print(shoppingList[5]) // Output - Hair Oil
```

Accessing an element with an invalid index causes a runtime crash; check `indices` or otherwise guard against out-of-bounds access.

You can access and modify a range of elements as well.

```swift
shoppingList[..<0] = ["Bread", "Chocolate"] // Inserts the sequence before the first element of the array
print(shoppingList[0...2]) // Output - ["Bread", "Chocolate", "Milk"]

shoppingList.insert(contentsOf: ["Butter", "Maple Syrup"], at: 0)
print(shoppingList[0...2]) // Output - ["Butter", "Maple Syrup", "Bread"]
```

You can remove elements using the remove(at:) method —

```swift
let firstElement = shoppingList.remove(at: 0)
print(shoppingList[0...1]) // Output - ["Maple Syrup", "Bread"]
```

The `remove(at:)` method returns the element that was removed.

#### Iterating over Arrays

```swift
for item in shoppingList {
    print(item, terminator: " ")
}
for (index, item) in shoppingList.enumerated() {
    print("Item \(index + 1): \(item)")
}
```

---

### Sets

Unordered collection of **unique** values of **same type** where the type stored should conform to **Hashable** protocol.

#### Creating Sets

```swift
var set0 = Set<Int>()
var set1: Set<Int> = []
var set2: Set = [1, 2, 3, 4]
print(type(of: set0)) // Output — Set<Int>
print(type(of: set1)) // Output — Set<Int>
print(type(of: set2)) // Output — Set<Int>
```
You cannot create an empty `Set` using `[]` without a type annotation; when you include values in the literal the compiler can infer a `Set` type.

While creating a set from arrays, it automatically removes any duplicate values.

```swift
var set3: Set = [1, 2, 3, 2, 1]
print(set3) // Output [1, 3, 2]

var shoppingListSet: Set = ["Milk", "Eggs", "Paint", "Fruits", "Eggs"]
print(shoppingListSet.count) // Output - 4
```

#### Accessing and Modifying Sets

```swift
shoppingListSet.insert("Chocolate")
print(shoppingListSet.count) // Output - 5
shoppingListSet.insert("Milk") // Inserting an element that already exists
print(shoppingListSet.count) // Output - 5

shoppingListSet.remove("Paint") // Output - Paint
print(shoppingListSet.contains("Paint")) // Output - false
```

As seen from the example, the `remove()` method returns the removed value if it exists otherwise it returns `nil`.

#### Iterating over Sets

```swift
for items in shoppingListSet {
    print(items, terminator: " ")
}
for items in shoppingListSet.sorted() {
    print(items, terminator: " ")
}
```

The first loop would print the elements of the set in a random manner. If you run the code repeatedly, it might end up different each time. But the second loop uses the sorted version of the set which would then print the elements in ascending order.

#### Set Operations

You can perform some standard set operations like union, intersection, symmetric difference and subtraction. You can also check for if a set is a subset, superset or disjoint with another set.

```swift
let colors: Set = ["Red", "Purple", "Orange"]
let fruits: Set = ["Apple", "Mango", "Orange"]

print(colors.union(fruits)) // Output - ["Mango", "Purple", "Orange", "Red", "Apple"]
print(colors.intersection(fruits)) // Output - ["Orange"]
print(colors.symmetricDifference(fruits)) // Output - ["Mango", "Purple", "Red", "Apple"]
print(colors.subtracting(fruits)) // Output - ["Purple", "Red"]

let rainbowColors: Set = ["Purple", "Blue", "Green", "Yellow", "Orange", "Red"]

print(colors.isSubset(of: rainbowColors)) // Output - true
print(rainbowColors.isSuperset(of: colors)) // Output - true
print(colors.isDisjoint(with: fruits)) // Output - false
```

---

### Dictionaries

Unordered collection of **key-value** pairs where the keys are of the **same type** and the values are of the same type as well. The keys need to conform to **Hashable** protocol just like for Sets.

#### Creating Dictionaries

```swift
let dict0 = Dictionary<Int, String>()
let dict1: Dictionary<Int, String> = [:]
let dict2: [Int: String] = [:]
let dict3 = [1: "1"] // Dictionary Literal
print(type(of: dict0)) // Output - Dictionary<Int, String>
print(type(of: dict1)) // Output - Dictionary<Int, String>
print(type(of: dict2)) // Output - Dictionary<Int, String>
print(type(of: dict3)) // Output - Dictionary<Int, String>
```

You can create a Dictionary via literal as well like you did for `dict3`.

#### Accessing and Modifying Dictionaries

```swift
var numbers = [
1: "One",
2: "Two",
4: "Four",
7: "Seven",
5: "Five"
]
```
Accessing the count of key-value pairs —

```swift
print(numbers.count) // Output - 5
print(numbers[5]) // Output - Optional("Five")
```

Note: Accessing a dictionary value by key returns an optional. This prevents crashes when a key is missing; you'll learn more about optionals in the dedicated article.

Updating values —

```swift
numbers[9] = "Nine"
let oldNum = numbers.updateValue("Eleven", forKey: 11)
```

Removing values —

```swift
numbers[2] = nil // Removes the key-value pair for key - 2
numbers.removeValue(forKey: 4)
print(numbers.count) // Output - 5
```

#### Iterating over Dictionaries

You can iterate over both the key-value pairs together, or individually you can iterate over the keys or the values as well.

Iterating over key-value pair —

```swift
for (numberInt, numberString) in numbers {
    print("String for \(numberInt) is \(numberString)")
}
```

Iterating over keys —

```swift
for numberInt in numbers.keys {
    print(numberInt, terminator: " ")
}
```

Iterating over values —

```swift
for numberString in numbers.values {
    print(numberString, terminator: " ")
}
```

---

Producing and maintaining these guides takes time and resources. If you found this article helpful and would like to support future content, consider a small contribution via [Buy Me a Coffee](https://buymeacoffee.com/swiftsimplified). Contributions help cover hosting and creation costs and make it possible to keep publishing free, practical material. No pressure — sharing this post with your network or starring the project is equally appreciated. Thank you for reading.