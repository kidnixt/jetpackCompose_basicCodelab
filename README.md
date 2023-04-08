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
