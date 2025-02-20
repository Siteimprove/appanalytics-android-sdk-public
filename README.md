# Siteimprove Analytics SDK

The Siteimprove Analytics SDK allows developers to easily track user interactions within their mobile applications. It provides automatic tracking of Activities, Fragments, and support for Compose screens, as well as manual tracking of various events. This guide will walk you through the steps to install, configure, and use the SDK effectively in your Android project.

**Table of contents:**
- [Siteimprove Analytics SDK](#siteimprove-analytics-sdk)
  - [Installation](#installation)
    - [Gradle (Groovy)](#gradle-groovy)
    - [Gradle (Kotlin DSL)](#gradle-kotlin-dsl)
  - [SDK Initialization](#sdk-initialization)
    - [Examples](#examples)
  - [Automatic Event Tracking](#automatic-event-tracking)
    - [Application Events](#application-events)
    - [Screen Events](#screen-events)
    - [Compose Applications](#compose-applications)
    - [Automatic Caching of Failed Events](#automatic-caching-of-failed-events)
  - [Manual Event Tracking](#manual-event-tracking)
    - [Track Application State](#track-application-state)
    - [Track Screen Views](#track-screen-views)
    - [Track Search Events](#track-search-events)
    - [Track Custom Events](#track-custom-events)


## Installation

The Siteimprove Analytics SDK is hosted on Maven Central. To integrate it into your Android project, follow the instructions below.

### Gradle (Groovy)
Add the following dependency to your `build.gradle` file:

```groovy
dependencies {
    implementation 'com.siteimprove:analyticssdk:1.1.0'
}
```

### Gradle (Kotlin DSL)
For projects using Kotlin DSL, add the following dependency to your `build.gradle.kts` file:

```kotlin
dependencies {
    implementation("com.siteimprove:analyticssdk:1.1.0")
}
```

## SDK Initialization
To start using the SDK, you must initialize it in your application's `Application` class. This is typically done in the `onCreate` method. You will need to provide your API key and specify the region (e.g., `Region.Region1`).

### Examples

**Java**:

```java
public class MyApp extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        Siteimprove.init(application = this, apiKey = "<api-key>", region = Region.Region1);
    }
}
```

**Kotlin**:
```kotlin
class SampleApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        Siteimprove.init(application = this, apiKey = "<api-key>",  region = Region.Region1)
    }
}
```

Replace "<api-key>" with your actual API key and the corresponding region (Region1 or Region2) provided within the Siteimprove platform. If API key and region is not correct no data will be collected.

## Automatic Event Tracking
The SDK automatically tracks key application lifecycle events, including when the app is started, sent to the background, or brought to the foreground. These events are captured without requiring any additional setup.

### Application Events
* **Opened** – This event is recorded when the app is launched (logged as **opened**).
* **Closed** – This event is recorded when the app is closed (logged as **closed**).
* **Foreground** – This event is recorded when the app is brought to the foreground (logged as **put_in_foreground**).
* **Background** – This event is recorded when the app is sent to the background (logged as **put_in_background**).

### Screen Events
* **Shown** – This event is recorded when a screen is displayed (logged as **shown**).
* **Dismissed** – This event is recorded when a screen is dismissed (logged as **dismissed**).

### Compose Applications
For Compose applications, a `LifecycleAwareScreenTracker` Composable is provided to track screen visibility.

**Example:**
```kotlin
@Composable
fun DetailsScreen(navController: NavController) {
    LifecycleAwareScreenTracker(screenName = stringResource(id = R.string.details_screen))

    Scaffold { paddingValues ->
        Box(
            modifier = Modifier
                .fillMaxSize()
                .padding(paddingValues),
            contentAlignment = Alignment.Center
        ) {
            Text(text = stringResource(id = R.string.details_screen))
        }
    }
}
```

### Automatic Caching of Failed Events
Events that fail to be sent due to a lack of network connectivity are automatically cached by the SDK. Once an internet connection is available, the SDK will attempt to resend these cached events. This process happens in the background without requiring any additional setup. Note that there is no mechanism to indicate if an event has failed to send.

## Manual Event Tracking
In addition to automatic tracking, the SDK provides methods to manually dispatch various events. These events include application state changes, screen views, and search results.

### Track Application State
To track application state changes (e.g., app opened, app sent to background), you can use the following method:

```kotlin
Siteimprove.trackAppState(CollectDataStateChange.StateChange.Opened);
```

### Track Screen Views
If you need to track a screen manually, use the `trackScreen` method:

```kotlin
Siteimprove.trackScreen("Home", "Home Screen", CollectDataScreen.State.Shown);
```

### Track Search Events
To track search interactions within your app, use the `trackSearch` method. You can specify the query, indicate whether the search was successful, and provide the number of search results:

```kotlin
Siteimprove.trackSearch("Sample query", true, 5);
```

### Track Custom Events
To track application-specific events, use the `trackEvent` method. This method allows you to specify a unique key for the event (use lowercase, e.g., `cart.refresh`) and attach a map of attributes related to the event. Each attribute is provided as a key-value pair, with keys in lowercase and values as single items (not nested objects).

**Example:**
```kotlin
Siteimprove.trackEvent(key = "cart.refresh", attributes = mapOf(
    "guest" to "true",
    "currency" to "USD",
    "cart.item_count" to "10",
    "cart.value" to "249.99"
))