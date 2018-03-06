# Listeners

SpigotKt adds a new way of registering simple listeners:

(`listen` is an extension of `Plugin`)
```Kotlin
listen<BlockBreakEvent> {
    it.player.sendMessage("Block broken!")
}
```

This example listens for `BlockBreakEvent` and sends a message to the player every time they break a block.

The parameter provided to the body of `listen` (`it` by default) is the event.
`this` is a `KListener` which provides a method to unregister the listener:

```Kotlin
listen<BlockBreakEvent> {
    it.player.sendMessage("Block broken!")
    unregister()
}
```

This example listens for the next time anyone breaks a block, sends them a message, then unregisters itself.

## Coroutines

Another way of listening for events is to use a coroutine:

(`listenCoroutine` is an extension of `Plugin`)
```Kotlin
listenCoroutine {
    val blockBreak = next<BlockBreakEvent>()
    
    blockBreak.player.sendMessage("Block broken!")
}
```

This example waits for the next `BlockBreakEvent`, sends the player a message, "Block broken!", then unregisters itself.

You can also add filters to `next`:
```Kotlin
listenCoroutine {
    val chat = next<AsyncPlayerChatEvent> { message == "Hello!" }
    
    chat.player.sendMessage("Hello ${chat.player.name}!")
}
```

This example waits for the next `AsyncPlayerChatEvent` where the message is "Hello!".
