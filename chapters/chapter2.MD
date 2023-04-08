# Chapter 2

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
