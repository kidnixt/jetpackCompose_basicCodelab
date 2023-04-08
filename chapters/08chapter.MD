# Chapter 8
### Creating a performant lazy list
Change the default list value in the `Greetings` parameters to use another list constructor which allows to set the list size and fill it with the value contained in its lambda (here `$it` represents the list index):

```kotlin 
names: List<String> = List(1000) { "$it" }
```

This creates 1000 greetings, even the ones that don't fit in the screen. Obviously this is not performant. You can try to run it on an emulator (warning: this code might freeze your emulator).

To display a scrollable column we use a `LazyColumn`. `LazyColumn` renders only the visible items on screen, allowing performance gains when rendering a big list.

In its basic usage, the LazyColumn API provides an items element within its scope, where individual item rendering logic is written:

```kotlin 
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.items
...

@Composable
private fun Greetings(
    modifier: Modifier = Modifier,
    names: List<String> = List(1000) { "$it" } 
) {
    LazyColumn(modifier = modifier.padding(vertical = 4.dp)) {
        items(items = names) { name ->
            Greeting(name = name)
        }
    }
}
```

> Note: Make sure you import androidx.compose.foundation.lazy.items as Android Studio will pick a different items function by default.

![](https://developer.android.com/static/codelabs/jetpack-compose-basics/img/2e29949d9d9b8690.gif)
