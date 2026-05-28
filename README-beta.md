
# Mopinion Native Android SDK (Beta)

The Mopinion Native Android SDK is a fully native Kotlin SDK designed to collect user feedback inside Android applications based on custom events and deployment rules.

This beta version introduces a simplified API, Flow-based state handling, improved Compose support, metadata APIs, deployment refresh control, and better Flutter interoperability.

---

# Contents

- [Mopinion Native Android SDK (Beta)](#mopinion-native-android-sdk-beta)
- [Contents](#contents)
- [Release Notes](#release-notes)
  - [What's new in Beta](#whats-new-in-beta)
- [Installation](#installation)
  - [Step 1](#step-1)
  - [Step 2](#step-2)
- [Internet Permission](#internet-permission)
- [SDK Initialisation](#sdk-initialisation)
  - [Initialisation Parameters](#initialisation-parameters)
- [Triggering Events](#triggering-events)
- [Jetpack Compose Integration 🚀](#jetpack-compose-integration-)
  - [Form State Flow](#form-state-flow)
  - [Deployment State Flow](#deployment-state-flow)
  - [Metadata](#metadata)
    - [Set Metadata](#set-metadata)
    - [Remove Metadata](#remove-metadata)
    - [Remove all metadata](#remove-all-metadata)
    - [Read Metadata](#read-metadata)
    - [Internal metadata](#internal-metadata)
  - [Language Support](#language-support)
  - [Manual Deployment Refresh `NEW`](#manual-deployment-refresh-new)
    - [Flutter Integration](#flutter-integration)
- [Important Notes](#important-notes)

---

# Release Notes

## What's new in Beta

- Fully redesigned singleton API.
- Native Kotlin implementation.
- Flow-based `FormState` observation.
- Flow-based `DeploymentState` observation.
- Native and WebView form support.
- Jetpack Compose compatible.
- Improved lifecycle handling.
- Metadata management API.
- Flutter plugin version tracking support.
- ThemeMode support:
    - Light
    - Dark
    - System
- Manual deployment refresh support.

---

# Installation

## Step 1

Add the JitPack repository.

```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)

    repositories {
        google()
        mavenCentral()

        maven {
            url = uri("https://jitpack.io")
        }
    }
}
```

## Step 2

Install the SDK in your module-level build.gradle.

```kotlin
dependencies {
    implementation("com.github.Mopinion-com.native-android-sdk:mopinion-sdk:BETA_VERSION")
}
```
For the WebView SDK:
```kotlin
dependencies {
    implementation("com.github.Mopinion-com.native-android-sdk:webview-sdk:BETA_VERSION")
}
```
Minimum supported Android API: 21

# Internet Permission

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

# SDK Initialisation

Initialise the SDK once inside your Application class or your main Activity.

```kotlin
Mopinion.initialise(
    application = this,
    deploymentKey = "YOUR_DEPLOYMENT_KEY",
    themeMode = ThemeMode.System,
    log = true
)
```

## Initialisation Parameters
| Parameter | Type | Description |
|---|---|---|
| application | Application | Android application instance. |
| deploymentKey | String | Deployment key from Mopinion platform. |
| themeMode | ThemeMode | Light, Dark or System. |
| log | Boolean | Enables SDK logging. |
| isFlutter | Boolean | Enables Flutter plugin support. |
| pluginVersion | String | Flutter plugin version. |

# Triggering Events

```kotlin
Mopinion.event(
    activity = this,
    eventName = "CheckoutCompleted"
)
```

The SDK evaluates deployment rules and determines whether a form should be shown.

# Jetpack Compose Integration 🚀

Your activity must extend from FragmentActivity or AppCompatActivity.
```kotlin
class MainActivity : FragmentActivity()
```

Inside your composable:
```kotlin
@Composable
fun FeedbackButton() {
    val activity = LocalContext.current as FragmentActivity

    Button(
        onClick = {
            Mopinion.event(
                activity = activity,
                eventName = "FeedbackButtonClicked"
            )
        }
    ) {
        Text("Open Feedback")
    }
}
```

## Form State Flow

The SDK exposes a StateFlow<FormState?> where you can check the form status.

```kotlin
Mopinion.formState
```

Example:

```kotlin
lifecycleScope.launch {
    Mopinion.formState.collect { state ->
        when (state) {
            is FormState.FormSent -> {
                // Form successfully submitted
            }

            FormState.FormCanceled -> {
                // User dismissed form
            }

            is FormState.Error -> {
                // Handle error
            }

            else -> Unit
        }
    }
}
```

## Deployment State Flow
The SDK exposes a StateFlow<DeploymentState?> where you can check the deployment status.

```kotlin
Mopinion.deploymentState
```

Example:

```kotlin
lifecycleScope.launch {
    Mopinion.deploymentState.collect { deploymentState ->
        // Observe deployment updates
    }
}
```

## Metadata

### Set Metadata
```kotlin
Mopinion.metadataAction(
    MetadataAction.Set(
        key = "user_id",
        value = "12345"
    )
)
```

### Remove Metadata
```kotlin
Mopinion.metadataAction(
    MetadataAction.RemoveData("user_id")
)
```

### Remove all metadata
```kotlin
Mopinion.metadataAction(
    MetadataAction.RemoveAllData
)
```
### Read Metadata
```kotlin
val metadata = Mopinion.getMetadata()
```

### Internal metadata
```kotlin
val internalMetadata = Mopinion.getInternalMetadata()
```

## Language Support

By default, the SDK uses the device language.
```kotlin
Mopinion.setLanguage("en")
```

## Manual Deployment Refresh `NEW`
```kotlin
Mopinion.refreshDeployment()
```

### Flutter Integration
```kotlin
Mopinion.initialise(
    application = this,
    deploymentKey = "YOUR_DEPLOYMENT_KEY",
    isFlutter = true,
    pluginVersion = "1.0.0"
)
```

# Important Notes

* Mopinion.event() requires a FragmentActivity.
* Forms are lifecycle aware.
* Only one form can be opened simultaneously.
* Deployment rules are evaluated automatically.
* The deprecated callback-based event() overload will be removed in future releases.
* Prefer observing Mopinion.formState instead of callback listeners.
* Native forms and WebView forms are selected automatically by the SDK.
