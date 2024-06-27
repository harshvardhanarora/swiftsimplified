# Swift Data
> iOS 17.0+ | iPadOS 17.0+ | macOS 14.0+ | watchOS 10.0+

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

## Attributes

```swift
macro Attribute(
    _ options: Schema.Attribute.Option...,
    originalName: String? = nil,
    hashModifier: String? = nil
)
```
### Parameters

`options` - List of options to apply. [Possible values](https://developer.apple.com/documentation/swiftdata/schema/attribute/option).\
`originalName` - Previous name of the attribute.\
`hashModifier` - Unique hash that represents the most recent version of the attached property.

## Relationship

```swift
macro Relationship(
    _ options: Schema.Relationship.Option...,
    deleteRule: Schema.Relationship.DeleteRule = .nullify,
    minimumModelCount: Int? = 0,
    maximumModelCount: Int? = 0,
    originalName: String? = nil,
    inverse: AnyKeyPath? = nil,
    hashModifier: String? = nil
)
```
### Parameters

`options` - List of options to apply.\
`deleteRule` - Delete rule when you delete the owner. [Possible values](https://developer.apple.com/documentation/swiftdata/schema/relationship/deleterule-swift.enum).\
`minimumModelCount` - Minimum number of models relationship can inference.\
`maximumModelCount` - Maximum number of models relationship can inference.\
`originalName` - Previous name of the attribute.\
`inverse` - Keypath that represents inverse of the relationship.\
`hashModifier` - Unique hash that represents the most recent version of the attached property.
> When we define a relationship attribute as optional, SwiftData only enforces the minimumDataCount and maximumDataCount only if it is not nil.

## Configure Storage
Use the `modelContainer` view modifier on the top level view to specify array/arrays to persist.

```swift
func modelContainer(
    for modelType: any PersistentModel.Type,
    inMemory: Bool = false,
    isAutoSaveEnabled: Bool = true,
    isUndoEnabled: Bool = false,
    onSetup: @escaping (Result<ModelContainer, any Error> -> Void = { _ in })
) -> some View
```
### Parameters
`modelType` - Type of model to be stored.\
`inMemory` - Whether to store the data in memory only.\
`onSetup` - Callback when model container creation succeeds or fails.


> For array of models, there is another modifier with the `modelType` changing to `modelTypes` and the type being an array of `PersistentModel`.

> Use `undoManager` in environment to manage undo operations.

### Example

```swift
ContentView()
    .modelContainer(for: [
        Group.self,
        Item.self
    ])
```

We can also create the container manually - 

```swift
let container = try ModelContainer([
    Group.self,
    Item.self
])
```