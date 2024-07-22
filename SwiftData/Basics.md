## Basics

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

You can mark a property `@Transient` if you don't want to persist that property in memory.