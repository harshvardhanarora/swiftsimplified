## Model Container

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