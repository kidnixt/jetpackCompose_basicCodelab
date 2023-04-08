# Chapter 7

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
