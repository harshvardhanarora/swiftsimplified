## Saving Data

A model context can be accessed from a SwiftUI view by using the environment property - 

```swift
import SwiftUI
import SwiftData

struct SomeView: View {
    @Environment(\.modelContext) private var context   
}
```

If you are trying to access the property from somewhere else, you can get the context from the container -

```swift
var context = container.mainContext
```

The context periodically checks for unsaved changes and then saves them. If you create the context manually, set the `autoSavedEnabled` to true to get the same behavior.

You can use the context to save data - 

```swift
let first = Group(name: "Paris Trip")
context.insert(first)
```