# Chapter 3

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
