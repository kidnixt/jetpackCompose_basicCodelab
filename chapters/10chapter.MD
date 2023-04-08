# Chapter 10

### Animating your list

For this you'll use the `animateDpAsState` composable. It returns a State object whose `value` will continuously be updated by the animation until it finishes. It takes a "target value" whose type is `Dp`.

Create an animated `extraPadding` that depends on the expanded state. Also, let's use the property delegate (the `by` keyword):
```kotlin 

@Composable
private fun Greeting(name: String) {

    var expanded by remember { mutableStateOf(false) }

    val extraPadding by animateDpAsState(
        if (expanded) 48.dp else 0.dp
    )
    ....
```


`animateDpAsState` takes an optional `animationSpec` parameter that lets you customize the animation. Let's do something more fun like adding a spring-based animation:
```kotlin 
val extraPadding by animateDpAsState(
        if (expanded) 48.dp else 0.dp,
        animationSpec = spring(
            dampingRatio = Spring.DampingRatioMediumBouncy,
            stiffness = Spring.StiffnessLow
        )
    )

    Surface(
    ...
            Column(modifier = Modifier
                .weight(1f)
                .padding(bottom = extraPadding.coerceAtLeast(0.dp))

    ...

    )
```

Any animation created with `**animate*AsState**` is interruptible. This means that if the target value changes in the middle of the animation, `**animate*AsState**` restarts the animation and points to the new value. Interruptions look especially natural with spring-based animations:

![](https://developer.android.com/static/codelabs/jetpack-compose-basics/img/e7d244875b3b6b6f.gif)
