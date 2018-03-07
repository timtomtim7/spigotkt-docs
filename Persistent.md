# Persistent Data

SpigotKt provides various objects with a `persistent` value that can be used to store data that will *persist* through server restarts.

The currently supported objects are:
* `Server`
* `OfflinePlayer`
* `Block`
* `Chunk`
* `World`

Obtain a `PersistentStorage` object by accessing the `persistent` property on an object of any type listed above.
```Kotlin
val obj = //some instance of one of the types listed above
val persistent = obj.persistent
```

`PersistentStorage` uses `Compound`s to store data.
To access a compound, you can use either a `Plugin` or a `String` as the key:

*It is recommended that you use your plugin instead of a string.*

```Kotlin
val compound = persistent.getOrCreate(plugin)
```

This example will get the compound for your plugin, or create it if it doesn't exist.

Other methods include
* `get`
* `isPresent`
* `edit`

*Warning: Do not manually cache the value returned by any of these methods.*

An example of using `edit`:
```Kotlin
persistent.edit(plugin) {
    int("test", 5)
}
```

And then retrieving it:
```Kotlin
val test = persistent[plugin]?.int("test") // 5 (Int?)
```
