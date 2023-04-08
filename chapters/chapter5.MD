# Chapter 5

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
