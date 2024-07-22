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