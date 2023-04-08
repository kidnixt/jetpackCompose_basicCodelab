# Codelab
## _Jetpack Compose basics_

* What compose is
* how to build UIs with compose
* How to manage state in composable functions
* How to create a performant list
* How to add animations
* How to style and theme an app

------------------------------------

### Composable
A **composable function** is a regular function annotated with `@Composable`. This enables your function to call other `@Composable` functions within it

> **Note:** Composable functions are Kotlin functions that are marked with the `@Composable` annotation, as you can see in the code snippet above.

------------------------------------

### Modifier

Most Compose UI elements such as `Surface` and `Text` accept an optional `modifier` parameter. Modifiers tell a UI element how to lay out, display, or behave within its parent layout.

For example, the `padding` modifier will apply an amount of space around the element it decorates. You can create a padding modifier with `Modifier.padding().`

```kotlin
@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Text(text = "Hello $name!", modifier = Modifier.padding(24.dp))
    }
}
```

> **Note:** 
> * Modifiers can have overloads so, for example, you can specify different ways to create a padding.
> * To add multiple modifiers to an element, you simply chain them.

------------------------------------

### Colmuns and rows
![](https://developer.android.com/static/codelabs/jetpack-compose-basics/img/518dbfad23ee1b05_1440.png)

They are Composable functions that take Composable content, so you can place items inside. For example, each child inside of a `Column` will be placed vertically.

```kotlin
@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colorScheme.primary) {
        Column(modifier = Modifier.padding(24.dp)) {
            Text(text = "Hello,")
            Text(text = name)
        }
    }
}
```
------------------------------------

### Button
In the next step you'll add a clickable element that expands the Greeting, so we need to add that button first. The goal is to create the following layout:

![](https://developer.android.com/static/codelabs/jetpack-compose-basics/img/eaf45a8dc0271a6f_1440.png)

`Button` is a composable provided by the material3 package which takes a composable as the last argument. Since trailing lambdas can be moved outside of the parentheses, you can add any content to the button as a child. For example, a `Text`:

```kotlin
Button(
    onClick = { } // You'll learn about this callback later
) {
    Text("Show less")
}
```

To achieve this you need to learn how to place a composable at the end of a row. **There's no `alignEnd` modifier so, instead, you give some `weight` to the composable at the start.** The `weight` modifier makes the element fill all available space, making it flexible, effectively pushing away the other elements that don't have a weight, which are called inflexible. It also makes the `fillMaxWidth` modifier redundant.

```kotlin

@Composable
private fun Greeting(name: String) {

    Surface(
        color = MaterialTheme.colorScheme.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            ElevatedButton(
                onClick = { /* TODO */ }
            ) {
                Text("Show more")
            }
        }
    }
}

```

------------------------------------

### State in Compose

Before getting into how to make a button clickable and how to resize an item, you need to store some value somewhere that indicates whether each item is expanded or not–the **state** of the item. Since we need to have one of these values per greeting, the logical place for it is in the `Greeting` composable. Take a look at this `expanded` boolean and how it's used in the code:

```kotlin
@Composable
private fun Greeting(name: String) {
    var expanded = false // Don't do this!

    Surface(
        color = MaterialTheme.colorScheme.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            ElevatedButton(
                onClick = { expanded = !expanded }
            ) {
                Text(if (expanded) "Show less" else "Show more")
            }
        }
    }
}
```

Note that we also added an onClick action and a dynamic button text. More on that later.

However, **this won't work as expected**. Setting a different value for the `expanded` variable won't make Compose detect it as a _state change_ so nothing will happen.

> Compose apps transform data into UI by calling composable functions. If your data changes, Compose re-executes these functions with the new data, creating an updated UI—this is called **recomposition**. Compose also looks at what data is needed by an individual composable so that it only needs to recompose components whose data has changed and skip recomposing those that are not affected.

> _Composable functions can execute frequently and in any order, you must not rely on the ordering in which the code is executed, or on how many times this function will be recomposed._ 

The reason why mutating this variable does not trigger recompositions is that **it's not being tracked by Compose.** Also, each time `Greeting` is called, the variable will be reset to false.

To add internal state to a composable, you can use the `mutableStateOf` function, which makes Compose recompose functions that read that `State`.

However **you can't just assign `mutableStateOf` to a variable inside a composable.** As explained before, recomposition can happen at any time which would call the composable again, resetting the state to a new mutable state with a value of `false`.

To preserve state across recompositions, remember the mutable state using `remember`.

`remember` is used to **guard** against recomposition, so the state is not reset.

Note that if you call the same composable from different parts of the screen you will create different UI elements, each with its own version of the state. **You can think of internal state as a private variable in a class.**

------------------------------------

### Mutating state and reacting to state changes

In order to change the state, you might have noticed that `Button` has a parameter called onClick but it doesn't take a value, **it takes a function.** 

You can define the action to take on click by assigning a lambda expression to it. For example, let's toggle the value of the expanded state, and show a different text depending on the value.

```kotlin
ElevatedButton(
    onClick = { expanded.value = !expanded.value },
) {
   Text(if (expanded.value) "Show less" else "Show more")
}
```

If you run the app in an emulator you can see that, when the button is clicked, `expanded` is toggled triggering a recomposition of the text inside the button. Each `Greeting` maintains its own expanded state, because they belong to different UI elements.

![](https://developer.android.com/static/codelabs/jetpack-compose-basics/img/f0edd5dc6d108de.gif)

------------------------------------

### Expanding the item

Now let's actually expand an item when requested. Add an additional variable that depends on our state:

```kotlin
@Composable
private fun Greeting(name: String) {

    val expanded = remember { mutableStateOf(false) }

    val extraPadding = if (expanded.value) 48.dp else 0.dp
...
```

You don't need to remember `extraPadding` against recomposition because it's doing a simple calculation.

And now we can apply a new padding modifier to the Column:

```kotlin
Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier
                .weight(1f)
                .padding(bottom = extraPadding)
            ) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            ElevatedButton(
                onClick = { expanded.value = !expanded.value }
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
        }

```

------------------------------------
