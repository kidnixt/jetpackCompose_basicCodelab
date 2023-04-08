# Chapter 6

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
