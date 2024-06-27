# Swift Data
> iOS 17.0+ | iPadOS 17.0+ | macOS 14.0+ | watchOS 10.0+

Attach `@Model` to any model class to make it persistable. At build time, the macro explands to provide conformance to `PersistentModel` and `Observable`. 

```swift
@Model
class Expense {
    var name: String
    var description: String
    var amount: Int
}
```

By default, SwiftData includes all non-computed properties of a class as long as they use compatible types. The framwork supports primitve types like `Bool`, `Int` and `String` as well as structures and enumerations as long as they conform to `Codable`.

## Attributes

```swift
macro Attribute(
    _ options: Schema.Attribute.Option...,
    originalName: String? = nil,
    hashModifier: String? = nil
)
```
**options** - List of options to apply. [Possible values](https://developer.apple.com/documentation/swiftdata/schema/attribute/option).\
**originalName** - Previous name of the attribute.\
**hashModifier** - Unique hash that represents the most recent version of the attached property.

## Relationship

```swift
macro Relationship(
    
)
```