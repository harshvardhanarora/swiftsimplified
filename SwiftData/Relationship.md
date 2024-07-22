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