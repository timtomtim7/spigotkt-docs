# NBT

SpigotKt adds an [NBT](https://minecraft.gamepedia.com/NBT_format) API.
Minecraft uses NBT for a lot of things including items, [block entities](https://minecraft.gamepedia.com/Block_entity), entities, player saves, and more.

## Basics

All NBT data starts with a `Compound`. A compound is basically a map of string names to values.

```Kotlin
val compound = Compound()
compound.int("test", 5)
compound.string("hello", "world")
```

```Kotlin
val compound = Compound {
    int("test", 5)
    string("hello", "world")
}
```

You can nest compounds inside other compounds:
```Kotlin
val compound = Compound {
    int("test", 5)
    string("hello", "world")
    compound("nested") {
        double("twoPointFour", 2.4)
    }
}
```

```Kotlin
val compound = Compound {
    int("test", 5)
    string("hello", "world")
    "nested" {
        double("twoPointFour", 2.4)
    }
}
```

To access data in a compound, use the same method you would to set it without a value:
```Kotlin
val test = compound.int("test") // 5 (Int?)
```

The return value is null if the value is not present in the compound or the value is not of the type requested:
```Kotlin
val test = compound.string("test") // null (String?)
```
The value named "test" is an Int

## Items

You can access item NBT:
```Kotlin
val item = // Some ItemStack
val nbt = item.nbt
nbt.int("someValue", 42)
item.nbt = nbt
```

or, more simply:
```Kotlin
val item = // Some ItemStack
item.editNBT {
    int("someValue", 42)
}
```

Modifying the value returned by `ItemStack#nbt` does not directly edit the item's NBT.
You have to reassign it, similar to `ItemMeta`.

## Blocks
If the block is a [block entity](https://minecraft.gamepedia.com/Block_entity), you can access it's NBT:

```Kotlin
val block = //some block
if(block.hasNBT()) {
    val nbt = block.nbt
    //edit NBT values
    block.nbt = nbt
}
```

If you attempt to access `Block#nbt` on a block that does not have NBT, it will throw an `IllegalArgumentException`.
You can use `Block#nbtOrNull` to access the NBT if it is present or `null`

Similar to items, there is `Block#editNBT`
```Kotlin
val block = //some block
if(block.hasNBT()) {
    block.editNBT {
        //edit NBT values
    }
}
```

**Important:** Items allow you to add arbitrary NBT values to them, blocks **do not**.
If you try to add an NBT value to a block entity that is not used by vanilla Minecraft, it will not stay.

## Types

The types you can directly add to a `Compound` include

* `Byte`
* `Short`
* `Int`
* `Long`
* `Double`
* `Float`
* `ByteArray`
* `String`
* `List` (all values must be of the same type and that type must be one of the other supported types)
* `Compound`
* `IntArray`

There are also a few types that can be serialized using the `Compound#serialized` methods:

*//This list is likely to be incomplete*
* `UUID`
* `World`
* `OfflinePlayer`
* `Location`
* `Block`

There is also a `serializedList` method for getting/setting lists of the above serializable types.
