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

### State Holding

In Composable functions, state that is read or modified by multiple functions should live in a common ancestor—this process is called **state hoisting**. To hoist means to lift or elevate.

Making state hoistable avoids duplicating state and introducing bugs, helps reuse composables, and makes composables substantially easier to test. Contrarily, state that doesn't need to be controlled by a composable's parent should not be hoisted. The source of truth belongs to whoever creates and controls that state.

The code added to create an onboarding screen contains a bunch of new features:
* `shouldShowOnboarding` is using a `by` keyword instead of the `=`. This is a property delegate that saves you from typing `.value` every time.
* When the button is clicked, `shouldShowOnboarding` is set to `false`, however you are not reading the state from anywhere yet.

> In Compose **you don't hide UI elements.** Instead, you simply don't add them to the composition, so they're not added to the UI tree that Compose generates. You do this with simple conditional Kotlin logic. For example to show the onboarding screen or the list of greetings you would do something like:

```kotlin 
@Composable
fun MyApp(modifier: Modifier = Modifier) {
    Surface(modifier) {
        if (shouldShowOnboarding) { // Where does this come from?
            OnboardingScreen()
        } else {
            Greetings()
        }
    }
}
``` 

However we don't have access to `shouldShowOnboarding.` It's clear that we need to share the state that we created in `OnboardingScreen` with the `MyApp` composable.

Instead of somehow sharing the value of the state with its parent, we **_hoist_** the state–we simply move it to the common ancestor that needs to access it.

We also need to share `shouldShowOnboarding` with the onboarding screen but we are not going to pass it directly. Instead of letting `OnboardingScreen` mutate our state, it would be better to let it notify us when the user clicked on the _Continue_ button.

How do we pass events up? By **passing callbacks down**. Callbacks are functions that are passed as arguments to other functions and get executed when the event occurs.

Try to add a function parameter to the onboarding screen defined as `onContinueClicked: () ->` Unit so you can mutate the state from MyApp.

```kotlin 
@Composable
fun MyApp(modifier: Modifier = Modifier) {

    var shouldShowOnboarding by remember { mutableStateOf(true) }

    Surface(modifier) {
        if (shouldShowOnboarding) {
            OnboardingScreen(onContinueClicked = { shouldShowOnboarding = false })
        } else {
            Greetings()
        }
    }
}

@Composable
fun OnboardingScreen(
    onContinueClicked: () -> Unit,
    modifier: Modifier = Modifier
) {


    Column(
        modifier = modifier.fillMaxSize(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text("Welcome to the Basics Codelab!")
        Button(
            modifier = Modifier
                .padding(vertical = 24.dp),
            onClick = onContinueClicked
        ) {
            Text("Continue")
        }
    }

}

```

By passing a function and not a state to `OnboardingScreen` we are making this composable more reusable and protecting the state from being mutated by other composables. In general, it keeps things simple.

------------------------------------

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

------------------------------------

### Persisting State
Our app has a problem: if you run the app on a device, click on the buttons and then you rotate, the onboarding screen is shown again. The `remember` function works **only as long as the composable is kept in the Composition.** When you rotate, the whole activity is restarted so all state is lost. This also happens with any configuration change and on process death.

Instead of using `remember` you can use `rememberSaveable`. This will save each state surviving configuration changes (such as rotations) and process death.

Now replace the use of `remember` in `shouldShowOnboarding` with `rememberSaveable`:
```kotlin 
var shouldShowOnboarding by rememberSaveable { mutableStateOf(true) }
```

------------------------------------

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

------------------------------------

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


### Replace button with an icon 
* Use the `IconButton` composable together with a child Icon.
* Use `Icons.Filled.ExpandLess` and `Icons.Filled.ExpandMore`, which are available in the `material-icons-extended` artifact. Add the following line to dependencies in your `app/build.gradle` file.

```kotlin 
implementation "androidx.compose.material:material-icons-extended:$compose_version"
```

### Use string resources
Content description for "Show more" and "show less" should be present and you can add them with a simple if statement. However, hard-coding strings is a bad practice and you should get them from the `strings.xml` file.

You can use "Extract string resource" on each string, available in "Context Actions" in Android Studio to do this automatically.

Alternatively, open `app/src/res/values/strings.xml` and add the following resources:

```kotlin 
<string name="show_less">Show less</string>
<string name="show_more">Show more</string>
```

### Showing more
The "Composem ipsum" text appears and disappears, triggering a change in size of each card
* Add a new `Text `to the Column inside `Greeting` that is displayed when the item is expanded.
* Remove the `extraPadding` and instead apply the `animateContentSize` modifier to the `Row`. This is going to automate the process of creating the animation, which would be hard to do manually. Also, it removes the need to `coerceAtLeast`.

### Add elevation and shapes 
You could use the `shadow` modifier together with `clip` modifier to achieve the card look. However, there's a Material composable that does exactly that: `Card`. You can change the Card's colors by calling `CardDefaults.cardColors`and overriding the color you want to change.
