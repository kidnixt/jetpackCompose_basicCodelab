# Chapter 12

### Replace button with an icon 
* Use the `IconButton` composable together with a child Icon.
* Use `Icons.Filled.ExpandLess` and `Icons.Filled.ExpandMore`, which are available in the `material-icons-extended` artifact. Add the following line to dependencies in your `app/build.gradle` file.

```kotlin 
implementation "androidx.compose.material:material-icons-extended:$compose_version"
```
----------------------------------------

### Use string resources
Content description for "Show more" and "show less" should be present and you can add them with a simple if statement. However, hard-coding strings is a bad practice and you should get them from the `strings.xml` file.

You can use "Extract string resource" on each string, available in "Context Actions" in Android Studio to do this automatically.

Alternatively, open `app/src/res/values/strings.xml` and add the following resources:

```kotlin 
<string name="show_less">Show less</string>
<string name="show_more">Show more</string>
```
----------------------------------------

### Showing more
The "Composem ipsum" text appears and disappears, triggering a change in size of each card
* Add a new `Text `to the Column inside `Greeting` that is displayed when the item is expanded.
* Remove the `extraPadding` and instead apply the `animateContentSize` modifier to the `Row`. This is going to automate the process of creating the animation, which would be hard to do manually. Also, it removes the need to `coerceAtLeast`.

----------------------------------------

### Add elevation and shapes 
You could use the `shadow` modifier together with `clip` modifier to achieve the card look. However, there's a Material composable that does exactly that: `Card`. You can change the Card's colors by calling `CardDefaults.cardColors`and overriding the color you want to change.
