# Commands

## Getting Started
Commands are quite a bit different when using SpigotKt.

The `command` function is available as an extension of `Plugin` and requires a name, a body, and an `onExectute` block.

```Kotlin
command("test") {
    onExecute {
        
    }
}
```

This will technically register a command, "test", but it won't do much.

To create a simple "Hello, world!" command:

```Kotlin
command("test") {
    onExecute {
        sender.sendMessage("Hello, world!")
    }
}
```

or

```Kotlin
command("test") {
    onExecute {
        reply("Hello, world!")
    }
}
```


`reply` is a method provided by `onExecute` that simply sends a message or messages to the sender.

Properties you can access include `sender`, `alias` (label), and `args` or `rawArgs`. More details on these below.

*Note: You cannot have a command without an `onExecute` block, it will error if you try.*

## Subcommands

A subcommand is a command within a command (ex. `/kit create`, `/kit delete` where "create" and "delete" are the subcommands)
To create subcommands:
```Kotlin
command("kit") {
    onExecute {
        subcommand("create") {
        
        }
        subcommand("delete") {
        
        }
        reply("Use one of the subcommands!")
    }
}
```

Inside the `subcommand` block you have access to the same things you had access to in `onExecute`.
The arguments (`args` and `rawArgs`) inside the subcommand block do not include the subcommand

Code after a `subcommand` block **will not** execute if the subcommand was executed.

*Note: "Use one of the subcommands!" is an example of a bad help message.*

You can nest subcommands inside other subcommands:
```Kotlin
command("why") {
    onExecute {
        subcommand("don't") {
            subcommand("go") {
                subcommand("too") {
                    subcommand("far") {
                        reply("Please.")
                    }
                }
            }
        }
    }
}
```

## Tab-Complete

When you press tab while typing in a command, it usually comes up with a list of suggestions.

You can edit this list by adding an `onTabComplete` block to your command:
```Kotlin
command("kit") {
    onExecute {
        //...
    }
    onTabComplete {
        subcommand("create")
        subcommand("delete")
        emptyList()
    }
}
```

The `onTabComplete` block must return a list of things to suggest.

Subcommands inside `onTabComplete` automatically add to the list.
The body for subcommands inside `onTabComplete` is optional.

*(If the last statement inside your `onTabComplete` is a `subcommand`, then you don't have to manually return a list as `subcommand` returns a list.)*

## Arguments

Arguments are values given to the command when it is executed. (ex. "/broadcast Hello" where "Hello" is the argument)
To access arguments inside either `onExecute`, `onTabComplete`, or `subcommand`, you have access to `args` and `rawArgs`.

* `rawArgs` is a list of the raw command arguments split by space
* `args` is an object representing the arguments that handles parsing

`args` provides a few methods:

* `next<T>(): T`
    * Tries to parse a value of T from the arguments. If this fails it will send the sender an error "Invalid usage: Expected T"            
    * Accepts an optional string argument for the expected value
* `optional<T>(): T?` - Tries to parse a value of T from the arguments. If this fails it will return null
* `isNext<T>(): Boolean` - Returns `true` if a value of `T` can be parsed from the arguments

A simple `multiply` command:
```Kotlin
command("multiply") {
    onExecute {
        val a = args.next<Int>()
        val b = args.next<Int>()

        reply((a * b).toString())
    }
}
```

When executing this command, you get the following result:
```
> multiply
Invalid usage: Expected Integer
> multiply 5
Invalid usage: Expected Integer
> multiply 5 4
20
```

To improve the error messages, we can provide a string to the `next` function:
```Kotlin
command("multiply") {
    onExecute {
        val a = args.next<Int>("the value to multiply")
        val b = args.next<Int>("the value to multiply by")

        reply((a * b).toString())
    }
}
```

Much better:
```
> multiply
Invalid usage: Expected the value to multiply
> multiply 5
Invalid usage: Expected the value to multiply by
> multiply 5 4
20
```

If you want to completely change the error message, you can do something like
```Kotlin
args.optional<Int>() ?: error("Usage: /multiply <a> <b>")
```

*//TODO: Allow completely changing the error message without using `optional`*

`error` is a method provided that sends a red message to the sender and immediately stops command execution.

## Details of `onExecute`

Inside the `onExecute` block, you have access to a few properties:
* `sender: CommandSender` - The sender of the command
* `alias: String` - The alias used
* `args: Arguments` - The arguments provided
* `rawArgs: List<String>` - The raw arguments provided, split by space.

And a few methods:
* `reply(message)` - Send a message to the sender.
* `error(message)` - Send a red message to the sender and stop executing the command.
* `fail()` - Immediately stop execution of the command.
* `fail {}` - Stop execution of the command after running the code inside the body.
* `subcommand(name, aliases...) {}` - Create a subcommand that runs the code inside the body. Examples of this below.
