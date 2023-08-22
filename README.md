[![](https://jitpack.io/v/Mopinion-com/native-android-sdk.svg)](https://jitpack.io/#Mopinion-com/native-android-sdk)

# Mopinion Native Android SDK

The Mopinion Native Android SDK has been developed 100% in native Kotlin, powerful tool to collect users
feedback from an Android App based on events.

### Contents

- [Mopinion Native Android SDK](#mopinion-native-android-sdk)
    - [Contents](#contents)
  - [Release notes for version 1.0.12](#release-notes-for-version-1012)
  - [Installation](#installation)
    - [Step 1:](#step-1)
    - [Step 2:](#step-2)
    - [Step 3:](#step-3)
    - [Step 4:](#step-4)
    - [On API versions \< 26 O (Oreo)](#on-api-versions--26-o-oreo)
  - [Implementing the SDK](#implementing-the-sdk)
  - [Kotlin](#kotlin)
  - [Java:](#java)
  - [Extra data](#extra-data)
  - [Clear Extra Data](#clear-extra-data)
    - [Example:](#example)
  - [Implementing FormState Callbacks](#implementing-formstate-callbacks)
    - [Kotlin Example:](#kotlin-example)
    - [Java Example:](#java-example)
  - [Flutter Integration](#flutter-integration)

## <a name="release_notes">Release notes for version 1.0.12</a>

The following reported issues have been solved:
- MOP-4114
- MOP-4077
- MOP-4085


## <a name="install">Installation</a>

- Install the Mopinion Native Android SDK Library by following the next steps.
- Download our [Mopinion Forms](https://play.google.com/store/apps/details?id=com.mopinion.mopinion) app
  from the Google Play Store to preview what your mobile forms will look like in your app.

### Step 1:

Add the JitPack repository to your `build.gradle` root file. Add it at the end of repositories:

```
allprojects {
		repositories {
		    ...
			maven { url 'https://jitpack.io' }
		}
	}
```

In newer versions of Android Studio this repository will have to be set in `settings.gradle` file of
the project as the following:

```
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven {
            ...
            url 'https://www.jitpack.io'
        }
    }
}
```

### Step 2:

Install the Mopinion Native Android SDK Library by adding it to the `build.gradle` module level of
your project. The minimal required Android API is 21.

```groovy
dependencies {
    implementation 'com.mopinion:native-android-sdk:1.0.12'
}
```

### Step 3:

The library needs to communicate with Mopinion Servers, please make sure you have the Internet
permission in your `AndroidManifest.xml`:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.app">
    
		<uses-permission android:name="android.permission.INTERNET" />
		
		<application
		...
```

### Step 4:

This SDK contains view components that extend from MaterialComponents. Your application theme needs to extend from a MaterialComponents theme. 
In case you can not change the application theme, and you have to use AppCompat themes mandatory, then you can use a MaterialComponent Bridge theme. 
Both Theme.MaterialComponents and Theme.MaterialComponents.Light have .Bridge themes:

- Theme.MaterialComponents.Bridge
- Theme.MaterialComponents.Light.Bridge
- Theme.MaterialComponents.NoActionBar.Bridge
- Theme.MaterialComponents.Light.NoActionBar.Bridge
- Theme.MaterialComponents.Light.DarkActionBar.Bridge

Bridge themes inherit from AppCompat themes, but also define the new Material Components theme attributes for you. If you use a bridge theme, you can start using Material Design components without changing your app theme.

For more information, please visit [MaterialComponents implementation steps](https://material.io/develop/android/docs/getting-started).

### On API versions < 26 O (Oreo)

This SDK implements Java LocalDateTime, which is not available on lower versions of Java 8 APIs, to be able to use it, you will have to apply the desugar plugin to your build.gradle (App module) as the following:
```groovy
defaultConfig {
        // Required when setting minSdkVersion to 20 or lower
        multiDexEnabled true
    }

compileOptions {
        coreLibraryDesugaringEnabled true
        ...
    }

    dependencies {
    ...
    coreLibraryDesugaring 'com.android.tools:desugar_jdk_libs:1.1.5'
    ...
}
```


## <a name="implement">Implementing the SDK</a>

## Kotlin

- In your MainActivity.kt, Mopinion object will be initialised, it will require two values, one
  AppCompatActivity or Activity and one String with the deployment key, as the following:

```kotlin
Mopinion.initialise(this, "$DEPLOYMENT_KEY")
```

- Once Mopinion is initialised, it is possible to instantiate Mopinion object wherever we need it,
  keep in mind that it`s processes will be lifecycle aware, to instantiate a Mopinion object it will
  require two values, an AppCompatActivity or FragmentActivity and a LifecycleOwner:
- Activity:

```kotlin
import com.mopinion.mopinion_android_sdk.*

class SomeActivity : AppCompatActivity() {
    private lateinit var mopinion: Mopinion

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        //This can be called from onCreate()
        mopinion = Mopinion(this)
        //Somewhere in the class where needed, .event can be called.
        mopinion.event("action") {
        }
    }
}
```

- Fragment:

```kotlin
import com.mopinion.mopinion_android_sdk.*

class SomeFragment : Fragment() {
    private lateinit var mopinion: Mopinion

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        //This can be called from onViewCreated()
        mopinion = Mopinion(requireActivity(), viewLifecycleOwner)
        //Somewhere in the class where needed, .event can be called.
        mopinion.event("action") {
        }
    }
}
```

## Java:

- In your MainActivity.java, Mopinion object will be initialised, it will require two values, one
  AppCompatActivity or Activity and one String form key, as the following:

```
Mopinion.Companion.initialise(this, "@FORM_KEY");
```

- Once Mopinion is initialised, it is possible to instantiate Mopinion object wherever we need it,
  keep in mind that it`s processes will be lifecycle aware, to instantiate a Mopinion object it will
  require two values, an AppCompatActivity or FragmentActivity and a LifecycleOwner:
- Activity:

```java
public class SomeActivity extends AppCompatActivity {

    private Mopinion mopinion;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.some_activity);

        mopinion = new Mopinion(this);
        mopinion.event("action", formState -> Unit.INSTANCE);
    }
}
```

- Fragment:

```java
public class SomeFragment extends Fragment {

    private Mopinion mopinion;

    @Override
    protected void onViewCreated(Bundle savedInstanceState) {

        mopinion = new Mopinion(requireActivity(), getViewLifecycleOwner());
        mopinion.event("action", formState -> Unit.INSTANCE);
    }
}
```

* The Activity will be used to extract the `applicationContext` and launch Kotlin Coroutines with
  the correct lifecycle scope.
* The `viewLifecycleOwner` will be used to implement the lifecycle safe coroutine launch
  method `repeatOnLifecycle()`.
* The key should be replaced with your specific deployment key. This key can be found in your
  Mopinion account at the `Feedback forms` section under `Deployments`.
* The `event` is a specific event that can be connected to a feedback form action in the Mopinion
  system. The default `action` event triggers the form, but an unlimited number of custom events can
  also be added.
* `event` is a lambda function, which receives a FormState Sealed Class containing the different
  FormStates, it also contains within its data classes:
    - FormSent contains `FeedbackPostModel` object with the data that has been sent.
    - FormError contains `hasErrors: Boolean` and the `errorMessage: String`.

## <a name="extra_data">Extra data</a>

It's also possible to send extra data from the app to your form. To do this, supply a key and a
value to the `data()` method. Add the data before calling the `event()` method if you want to
include the data in the form that comes up for that event.

```kotlin
mopinion.data(key as String, value as String)
```

```kotlin
import com.mopinion.mopinion_android_sdk.*

val mopinion = Mopinion(this / requireActivity(), "$FORM_KEY")
mopinion.data("first name", "Andy")
mopinion.data("last name", "Rubin")
...
mopinion.event(eventName as String)
```

## <a name="clear_extra_data">Clear Extra Data</a>

It's possible to remove all or a single key-value pair from the extra data previously supplied with
the `data(key,value)` method. To remove a single key-value pair use this method:

```kotlin
mopinion.removeData(key as String)
```

### Example:

```kotlin
mopinion.removeData("first name")
```

To remove all supplied extra data use this method without arguments:

```kotlin
mopinion.removeData()
```

## <a name="implementing_callbacks">Implementing FormState Callbacks</a>

The `event()` function is a lambda function which once has been executed, receives a Sealed Class
FormState. This behaviour is meant to indicate in which state the Form is. This class contains the
following States:
State|Methods|Description 
---|---|---
Loading|No methods|Is emitted when the form is loading.
FormOpened|No methods|Is emitted once the form is opened.
FormSent|.feedbackPostModel/.getFeedbackPostModel()|Is emitted once the user submits the form. This Object contains FeedbackPostModel, which is the form with the data the user has submitted.
FormCanceled|No methods|Is emitted when the form is canceled (tapping outside of the BottomSheetDialogFragment or the back arrow). 
FormClosed|No methods|Is emitted when the form is submitted and closes automatically, or when the form logic is set to auto-close when the form is submitted. 
Error|.message/.getMessage: String, .hasErrors/.getHasErrors(): Boolean|Is emitted when an error occurs when the form is being submitted.
HasNotBeenShown|.reason/getReason(): Reason|Is emitted when due to circunstances that can not be determined as errors occur. This state has as constructor a Reason which gives a clear reason of why the form is not showing up.

As mentioned in the HasNotBeenShown State, this state contains a Reason explaining why the form did not showed up. The possible reasons by the moment are the following:
Reason|Methods|Description
---|---|---
ErrorWhileFetchingForm|.exception/.getException(): Exception|This reason is provided when fetching the form an error occurs, it contains an Exception.
DoesNotMatchProactiveRules|No methods|This reason is provided when the form rules does not match the required criteria to be shown. 
EventDoesNotExist|No methods|The event provided does not exist on the deployment.
FormDoesNotExist|No methods|The form key required does not exist on backend, meaning the form key to which the GET call has been made returns an empty form. This can occur because the form has been deleted and the deployment has not been updated.

### Kotlin Example:

```kotlin
mopinion.event(event) { formState ->
    when (formState) {
        is FormState.Error -> {
            if (formState.hasErrors) {
                //do something...
                val errorMessage = formState.message
            }
        }
        FormState.FormCanceled -> {
            //do something...
        }
        FormState.FormClosed -> {
            //do something...
        }
        FormState.FormOpened -> {
            //do something...
        }
        is FormState.FormSent -> {
            //do something...
            val postedObject = formState.feedbackPostModel
            //postedObject is the object that contains the form data that has been submitted. 
        }
        FormState.Loading -> {
            //do something...
        }
        is FormState.HasNotBeenShown -> {
          //do something...
          //check why the form did not shown up:
          val reasonOfWhyDidNotShowUp = formState.reason
        }
    }
}
```
If some of the States will not be used, just set the `else` and empty curly braces:
```kotlin
mopinion.event(event) { formState ->
    when (formState) {
        is FormState.Error -> {
            if (formState.hasErrors) {
                //do something...
                val errorMessage = formState.message
            }
        }
        is FormState.FormSent -> {
            //do something...
            val postedObject = formState.feedbackPostModel
            //postedObject is the object that contains the form data that has been submitted. 
        }
        else -> {
            //do nothing.
        }
    }
}
```

### Java Example:
```java
class SomeClass extends AppCompatActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.some_activity);

    mopinion.event("action", formState -> {
      if (formState instanceof FormState.Loading) {
            //do something...
      }
      if (formState instanceof FormState.FormOpened) {
        //do something...
      }
      if (formState instanceof FormState.FormSent) {
        //do something...
        // So we can extract the posted object.
        FeedbackPostModel postedObject = ((FormState.FormSent) formState).getFeedbackPostModel();
      }
      if (formState instanceof FormState.FormCanceled) {
        //do something...
      }
      if (formState instanceof FormState.FormClosed) {
        //do something...
      }
      if (formState instanceof FormState.Error) {
        //do something...
        // It is possible to check if it has errors and extract the error message.
        if (((FormState.Error) formState).getHasErrors()) {
            String errorMessage = ((FormState.Error) formState).getMessage();
        }
      }
      if (formState instanceof FormState.HasNotBeenShown) {
        //do something...
        //check why the form did not show up:
        Reason reasonOfWhyDidNotShowUp = ((FormState.HasNotBeenShown) formState).getReason();

      }
      return Unit.INSTANCE;
    });
  }
}
```
If some of the States will not be used, just do not implement the if sentence. Always keep the return Unit.INSTANCE in Java.

## <a name="flutter">Flutter Integration</a>
To be able to use our Native SDK in a Flutter project, please go to the [Flutter Integration SDK repository](https://github.com/Mopinion-com/flutter-integration-sdk) and follow the instructions.