# Chapter 11

### Styling and theming your app

If you open the `ui/theme/Theme.kt` file, you see that `BasicsCodelabTheme` uses `MaterialTheme` in its implementation

`MaterialTheme` is a composable function that reflects the styling principles from the Material design specification. That styling information cascades down to the components that are inside its `content`, which may read the information to style themselves. In your UI, you are already using `BasicsCodelabTheme` as follows

Because `BasicsCodelabTheme` wraps `MaterialTheme` internally, `MyApp` is styled with the properties defined in the theme. From any descendant composable you can retrieve three properties of `MaterialTheme`: `colorScheme`, `typography` and `shapes`. Use them to set a header style for one of your Texts:

```kotlin 
Column(modifier = Modifier
                .weight(1f)
                .padding(bottom = extraPadding.coerceAtLeast(0.dp))
            ) {
                Text(text = "Hello, ")
                Text(text = name, style = MaterialTheme.typography.headlineMedium)
            }

```

The `Text` composable in the example above sets a new `TextStyle`. You can create your own TextStyle, or you can retrieve a theme-defined style by using `MaterialTheme.typography`, which is preferred. This construct gives you access to the Material-defined text styles, such as `displayLarge, headlineMedium, titleSmall, bodyLarge, labelMedium` etc. In your example, you use the `headlineMedium` style defined in the theme.

In general it's much better to keep your colors, shapes and font styles inside a MaterialTheme. For example, dark mode would be hard to implement if you hard-code colors and it would require a lot of error-prone work to fix.

For this, you can modify a predefined style by using the `copy` function. Make the number extra bold:

```kotlin 
Text(
    text = name,
    style = MaterialTheme.typography.headlineMedium.copy(
    fontWeight = FontWeight.ExtraBold))
```
---------------------------------

### Tweak your app's theme
You can find everything related to the current theme in the files inside the `ui/theme` folder. For example, the default colors that we have been using so far are defined in `Color.kt.`

Let's start by defining new colors. Add these to `Color.kt`:
```kotlin 
val Navy = Color(0xFF073042)
val Blue = Color(0xFF4285F4)
val LightBlue = Color(0xFFD7EFFE)
val Chartreuse = Color(0xFFEFF7CF)
```
Now assign them to the MaterialTheme's palette in `Theme.kt:`

```kotlin 

private val LightColorScheme = lightColorScheme(
    surface = Blue,
    onSurface = Color.White,
    primary = LightBlue,
    onPrimary = Navy
)

```
If you go back to `MainActivity.kt` and refresh the preview, the preview colors don't actually change! That's because by default, your Preview will use dynamic colors. You can see the logic for adding dynamic coloring in `Theme.kt`, using the `dynamicColor` boolean parameter.
