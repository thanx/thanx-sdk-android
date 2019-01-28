# Thanx Android SDK

The Thanx Android SDK provides a simple way to embed the Thanx customer
engagement experience in Android applications.

## Delivery

The SDK will be delivered as a Gradle package distributes via
[Thanx's Maven repo](https://bintray.com/thanx/sdk/thanx-sdk-android).

## Authentication

### Merchant

Thanx will provide the SDK `Client ID` and a `Client Secret` via a secure
channel.

Authentication of the SDK should be done with the provided SDK `Client ID` and 
`Client Secret`. The developer should initialize the SDK when the app launches
with those values.

### User

This SDK supports user authentication via access token or user login.

**Access Token Authentication**:

If an access token is provided on SDK initialization, the user and SDK will be
automatically authenticated.

**Manual Authentication**:

If no access token is provided on SDK initialization, a login form will be
displayed to the user in the webview where they will be prompted to login or
create an account.

**Post Authentication**

After authentication is completed through either mechanism, the user's details
(`email`, `firstName`, `lastName`) will be accessible via the SDK.

## Push Notifications

Thanx will need the FCM API key in order to send Loyalty related push
notifications to the user. The Thanx team will work with Westfield to provide a
mechanism for securely exchanging this information.

In the app, after the developer asks for push notifications permissions and the
user accepts, the developer should send the push registration token to the SDK
so the user gets the loyalty related push notifications that the Thanx platform
offers.

## User Experience

### Web flows

Thanx SDK user experience will be web based. When the user launches the rewards,
they'll be presented with a web view that will load the Thanx platform. There,
the user will be able to see and redeem rewards and link cards in order to
accrue progress when shopping at the stores.

## Integration Testing

The SDK will provide an option to run in test mode. In that mode, the web view
and the API will point to a dev environment where the developer will be able to
test the integration without affecting production data (more details when SDK
is delivered).

## Installation

Via Gradle

The **thanx-sdk-android** package lives in
[Thanx's Maven repo](https://bintray.com/thanx/sdk/thanx-sdk-android). For this
reason, the first thing you need to do is to add the repo url to your project's
build.gradle **respositories** section. Also, because of the dependencies used,
you will have to include the **jitpack** repo as well:

```groovy
// build.gradle
repositories {
  // ...Other repositories...

  maven { url "https://dl.bintray.com/thanx/sdk" }
  maven { url "https://jitpack.io" }
}
```

Then you can include the **thanx-sdk-android** package as one of your
dependencies in your module's build.gradle **dependencies** section:

```groovy
// build.gradle
dependencies {
  // ...Other dependencies...
  // Always use the latest version instead of 0.0.1
  implementation('com.thanx.sdk:thanx-sdk-android:0.0.1@aar') {
    transitive = true
  }
}
```

The SDK requires internet, and external storage and camera for the receipt
upload so make sure you add the following in your Manifest.xml

```xml
<!-- Manifest.xml -->
<uses-permission android:name="android.permission.INTERNET" />
<!-- Permission to read/write external storage - required -->
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<!-- Permission to use camera - required -->
<uses-permission android:name="android.permission.CAMERA"/>
```

## Initialization

You should initialize the SDK in the **MainActivity** file of your project:

1. Import the SDK at the top of the file **import com.thanx.sdk.Thanx**;
2. Call **Thanx.initialize** on the **onCreate** method

**Java**:

```java
// MainActivity.java
import com.thanx.sdk.Thanx;

public class MainActivity extends AppCompatActivity {

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    // Automatic User Authentication
    Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true, "USER_ACCESS_TOKEN");
    // Manual User Authentication
    Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true);
  }
}
```

**Kotlin**:

```kotlin
// MainActivity.kt
import com.thanx.sdk.Thanx

class MainActivity : AppCompatActivity() {

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    // Automatic User Authentication
    Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true, "USER_ACCESS_TOKEN")
    // Manual User Authentication
    Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true)
  }
}
```

The Thanx SDK is now initialized and ready for use.

<aside class="notice">
  Make sure to set <strong>debug: false</strong> for production builds.
</aside>

## Launching the Thanx experience

#### Launch as a new Activity

The Thanx SDK provides the WebActivity with the full Thanx mobile experience.
Use the following in order to launch the Activity:

**Java**:

```java
// Create the intent
Intent thanxWebView = new Intent(MainActivity.this, WebActivity.class);
// Launch the intent
startActivity(thanxWebView);

// Alternatively, you can use the helper method to launch the activity
// WebActivity.start(this, null);
```

```kotlin
Kotlin

// Create the intent
val thanxWebViewIntent = Intent(context, WebActivity::class.java)
// Launch the intent
startActivity(thanxWebViewIntent)

// Alternatively, you can use the helper method to launch the activity
// WebActivity.start(context)
```

#### Launch as a new Fragment

The Thanx SDK also provides the **WebFragment** with the full Thanx mobile
experience. Fragments are more flexible in terms on how you can display them.
For example you can attach them as a subview of one of your activities.

**Java**:

```kotlin
// Instantiate a new WebFragment
val webFragment = WebFragment.newInstance(null);

// Display the fragment however you want
getSupportFragmentManager()
  .beginTransaction()
  .add(R.id.web_view, webFrament, "thanx_web_fragment")
  .addToBackStack("thanx_web_fragment")
  .commit();
```

**Kotlin**:

```kotlin
// Instantiate a new WebFragment
val webFragment = WebFragment.newInstance()

// Display the fragment however you want
supportFragmentManager
  .beginTransaction()
  .add(R.id.fragment_container, webFragment, "thanx_web_fragment")
  .addToBackStack("thanx_web_fragment") // Add support for the back button to dismiss the fragment
  .commit()
```

## Push Notifications

### Device Token Registration

Thanx will send some push notifications to the user to remind him/her about
their progress towards their reward, to leave feedback, and when they achieve a
new reward. To make it work, the SDK needs to be notified about the device
token whenever the user registers a new device for push notifications:

**Java**:

```java
// Register the device token with Thanx
new PushNotificationManager(this).register(deviceToken); // where deviceToken is the new token generated by the OS.
```

**Kotlin**:

```kotlin
// Register the device token with Thanx
PushNotificationManager(context).register(deviceToken) // where deviceToken is the new token generated by the OS.
```

If you use Firebase, you'll need to hook this SDK call with the Firebase callback when the user receives a new token:

**Java**:

```java
// MyFirebaseMessagingService.java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
  @Override
  public void onNewToken(String token) {
    if (token == null) { return }
    // .... Your API call to register the token with your backend service ...

    // Register the new token with Thanx
    new PushNotificationManager(getApplicationContext()).register(token);
  }
}
```

**Kotlin**:

```kotlin
MyFirebaseMessagingService.kt
class MyFirebaseMessagingService: FirebaseMessagingService() {
  override fun onNewToken(newToken: String?) {
    newToken ?: return
    // .... Your API call to register the token with your backend service ...

    // Register the new token with Thanx
    PushNotificationManager(context = applicationContext).register(newToken)
  }
}
```

For more information, please refer to the
[Firebase documentation](https://firebase.google.com/docs/cloud-messaging/android/client#retrieve-the-current-registration-token).

### Push Notification Landing Screens

Thanx also delivers some contextual landing screens for most of the push
notifications. These screens are meant to give a bit more context to the user
about what just happened with their rewards and also contain very engaging call
to actions with very high yield (for example, to leave feedback about the most
recent visit). In order to enable these landing screens, the SDK needs to be
notified every time a new push notification arrives.

#### Via Activity

If you use the **WebActivity**, this time, you'll have to pass the notification
payload as extras:

**Java**:

```java
// Instantiate a new WebFragment passing the push notification payload
WebActivity.start(context, payload); // Where payload is a Map<String, String> that comes from the push notification
```

**Kotlin**:

```kotlin
// Instantiate a new WebFragment passing the push notification payload
WebActivity.start(context, payload) // Where payload is a Map<String, String> that comes from the push notification
```

If you use **Firebase**, you will need to place that SDK call into the
**onMessageReceived** method of your **FirebaseMessagingService** subclass:

**Java**:

```java
// MyFirebaseMessagingService.java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
  @Override
  public void onMessageReceived(RemoteMessage remoteMessage) {
    WebActivity.start(getApplicationContext(), remoteMessage.getData());
  }
}
```

**Kotlin**:

```kotlin
// MyFirebaseMessagingService.kt
class MyFirebaseMessagingService: FirebaseMessagingService() {
  override fun onMessageReceived(remoteMessage: RemoteMessage?) {
    WebActivity.start(applicationContext, remoteMessage?.data)
  }
}
```

For more information on how to hook up the landing screens with Firebase,
please refer to the
[Firebase documentation](https://firebase.google.com/docs/cloud-messaging/android/receive#override-onmessagereceived).

#### Via Fragment

If you use the *WebFragment*, this time, you'll need to instantiate it with the
notification payload:

**Java**:

```java
// Instantiate a new WebFragment passing the push notification payload
WebFragment webFragment = WebFragment.newInstance(payload);  // Where payload is a Map<String, String> that comes from the push notification

// Display the fragment however you want
.getSupportFragmentManager()
  .beginTransaction()
  .add(R.id.web_fragment_container, webFragment, "thanx_web_fragment")
  .addToBackStack("thanx_web_fragment")  // Add support for the back button to dismiss the fragment
  .commit();
```

**Kotin**:

```kotlin
// Instantiate a new WebFragment passing the push notification payload
val webFragment = WebFragment.newInstance(payload) // Where payload is a Map<String, String> that comes from the push notification

// Display the fragment however you want
supportFragmentManager
  .beginTransaction()
  .add(R.id.fragment_container, webFragment, "thanx_web_fragment")
  .addToBackStack("thanx_web_fragment") // Add support for the back button to dismiss the fragment
  .commit()
```

If you use **Firebase**, you will need to place that SDK call into the
**onMessageReceived** method of your **FirebaseMessagingService** subclass:

**Java**:

```java
//MyFirebaseMessagingService.java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
  @Override
  public void onMessageReceived(RemoteMessage remoteMessage) {
    // Instantiate a new WebFragment passing the push notification payload
    WebFragment webFragment = WebFragment.newInstance(remoteMessage.getData());

    // Display the fragment however you want
    ((FragmentActivity) getApplicationContext())
      .getSupportFragmentManager()
      .beginTransaction()
      .add(R.id.web_fragment_container, webFragment, "thanx_web_fragment")
      .addToBackStack("thanx_web_fragment")
      .commit();
  }
}
```

**Kotlin**:

```kotlin
MyFirebaseMessagingService.kt
class MyFirebaseMessagingService: FirebaseMessagingService() {
  override fun onMessageReceived(remoteMessage: RemoteMessage?) {
    // Instantiate a new WebFragment passing the push notification payload
    val webFragment = WebFragment.newInstance(remoteMessage?.data)

    // Display the fragment however you want
    (applicationContext as? FragmentActivity)
      ?.supportFragmentManager
      ?.beginTransaction()
      ?.add(R.id.web_fragment_container, webFragment, "thanx_web_fragment")
      ?.addToBackStack("thanx_web_fragment")
      ?.commit()
  }
}
```

For more information on how to hook up the landing screens with Firebase,
please refer to the
[Firebase documentation](https://firebase.google.com/docs/cloud-messaging/android/receive#override-onmessagereceived).

<aside class="notice">
  <strong>
    Make sure that the SDK is initialized before presenting the WebActivity or
    WebFragment
  </strong>
</aside>

## Debug Mode

In order to run the SDK in debug mode, you'll need to initialize it with the debug flag as true:

**Java**:

```java
// MainActivity.java
Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true);
Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true, "USER_ACCESS_TOKEN");
```

**Kotlin**:

```kotlin
MainActivity.kt
Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true)
Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true, "USER_ACCESS_TOKEN")
```

Running the SDK in debug will provide you with extra console output and most
importantly, it will point to the **Thanx Staging environment**.

**You should always run in Debug mode unless is a production build.**
