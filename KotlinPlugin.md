# KotlinPlugin

When using SpigotKt, you have access to `KotlinPlugin`, the SpigotKt/Kotlin alternative to `JavaPlugin`.
Extending `KotlinPlugin` forces you to use an `object` for your main class and doesn't require a `plugin.yml` file.

```Kotlin
@PluginDescription("AnExample", "1.0")
object AnExamplePlugin: KotlinPlugin() {
    override fun onEnable() {
        
    }
}
```

The first thing you may notice is the `PluginDescription` annotation, this is the replacement for the `plugin.yml`.
The required parameters for it are just your plugin name and version.

Your main class name **must** end in `Plugin` (ex. `AnExamplePlugin`, `TestPlugin`, `AutoSellPlugin`),
and your plugin must go in the `kt-plugins` folder, **not** in the normal `plugins` folder.
