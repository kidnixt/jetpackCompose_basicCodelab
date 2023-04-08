# Chapter 9

### Persisting State
Our app has a problem: if you run the app on a device, click on the buttons and then you rotate, the onboarding screen is shown again. The `remember` function works **only as long as the composable is kept in the Composition.** When you rotate, the whole activity is restarted so all state is lost. This also happens with any configuration change and on process death.

Instead of using `remember` you can use `rememberSaveable`. This will save each state surviving configuration changes (such as rotations) and process death.

Now replace the use of `remember` in `shouldShowOnboarding` with `rememberSaveable`:
```kotlin 
var shouldShowOnboarding by rememberSaveable { mutableStateOf(true) }
```
