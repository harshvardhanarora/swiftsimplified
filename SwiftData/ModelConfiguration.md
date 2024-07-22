## Model Configuration

Provides number of options to configure - 
- the storage exists in memory only
- the storage is read-only
- the app uses specific App Group for data storage

> Automatic iCloud sync relies on the presence of the CloudKit entitlement, and SwiftData uses the first container it finds in that entitlement. If your app needs a particular container, use an instance of `ModelConfiguration` to specify that container.