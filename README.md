# ⚠️ Deprecated ⚠️

This SDK has been deprecated and is no longer maintained. Please reach to success@thanx.com if you want to integrate with the Thanx platform.


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
notifications to the user. The Thanx team will work with the merchant to
provide a mechanism for securely exchanging this information.

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
    Thanx.initialize(this, "USER_ACCESS_TOKEN", true, true, null);
    // Manual User Authentication
    Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true, true, null);
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
    Thanx.initialize(context = this, accessToken = "USER_ACCESS_TOKEN", debug = false, logging = true, completion = null)
    // Manual User Authentication
    Thanx.initialize(context = this, clientId = "YOUR_CLIENT_ID", clientSecret = "YOUR_CLIENT_SECRET", debug = false, logging = true, completion = null)
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
```

```kotlin
Kotlin

// Create the intent
val thanxWebViewIntent = Intent(context, WebActivity::class.java)
// Launch the intent
startActivity(thanxWebViewIntent)
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

## Loading Callbacks

Callbacks are provided as an interface in order to know when the Thanx SDK mobile
experience is loading.

**Java**:

```java
interface LoadingListener {
  // Callback triggered when the initial load starts
  void onInitialLoadStart(String url);

  // Callback triggered when any load starts (including the initial load)
  // Note: It might be triggered multiple times for the same page
  void onLoadStart(String url);

  // Callback triggered when the initial load finishes
  void onInitialLoadFinish(String url);

  // Callback triggered when any load finishes (including the initial load)
  // Note: It might be triggered multiple times for the same page
  void onLoadFinish(String url);
}
```

**Kotlin**

```kotlin
interface LoadingListener {
  // Callback triggered when the initial load starts
  fun onInitialLoadStart(url: String?) {}

  // Callback triggered when any load starts (including the initial load)
  // Note: It might be triggered multiple times for the same page
  fun onLoadStart(url: String?) {}

  // Callback triggered when the initial load finishes
  fun onInitialLoadFinish(url: String?) {}

  // Callback triggered when any load finishes (including the initial load)
  // Note: It might be triggered multiple times for the same page
  fun onLoadFinish(url: String?) {}
}
```

#### Implementing the callbacks with WebActivity

In order to implement them with the WebActivity provided, a new activity that
inherits from WebActivity needs to be created implementing the `LoadingListener` interface.

**Java**:

```java
public class CustomWebActivity extends WebActivity implements LoadingListener {
  @Override
  public void onInitialLoadStart(@Nullable String url) {
    // Callback triggered when the initial load starts
    // E.g. show loading spinner
  }

  @Override
  public void onInitialLoadFinish(@Nullable String url) {
    // Callback triggered when the initial load finishes
    // E.g. hide loading spinner
  }

  @Override
  public void onLoadStart(@Nullable String url) {
    // Callback triggered when any load starts (including the initial load)
    // Note: It might be triggered multiple times for the same page
  }

  @Override
  public void onLoadFinish(@Nullable String url) {
    // Callback triggered when any load finishes (including the initial load)
    // Note: It might be triggered multiple times for the same page
  }
}
```

**Kotlin**:

```kotlin
class CustomWebActivity: WebActivity(), LoadingListener {
  override fun onInitialLoadStart(url: String?) {
    // Callback triggered when the initial load starts
    // E.g. show loading spinner
  }

  override fun onInitialLoadFinish(url: String?) {
    // Callback triggered when the initial load finishes
    // E.g. hide loading spinner
  }

  override fun onLoadFinish(url: String?) {
    // Callback triggered when any load starts (including the initial load)
    // Note: It might be triggered multiple times for the same page
  }

  override fun onLoadStart(url: String?) {
    // Callback triggered when any load finishes (including the initial load)
    // Note: It might be triggered multiple times for the same page
  }
}
```

This new custom activity needs to be registered in your Manifest.xml inside
the application keys.

```xml
<!-- Manifest.xml -->
<application
  android:allowBackup="true"
  android:icon="@mipmap/ic_launcher"
  android:label="@string/app_name"
  android:roundIcon="@mipmap/ic_launcher_round"
  android:supportsRtl="true"
  android:theme="@style/AppTheme"
>
  <!-- Your other activities... -->

  <!-- The new CustomWebActivity -->
  <activity android:name=".CustomWebActivity"></activity>
</application>
```

Then, you just need to launch the newly created activity (`CustomWebActivity`)
instead of the default `WebActivity`

**Java**:

```java
// Create the intent
Intent customThanxWebView = new Intent(MainActivity.this, CustomWebActivity.class);
// Launch the intent
startActivity(customThanxWebView);
```

**Kotlin**:

```kotlin
// Create the intent
val customThanxWebViewIntent = Intent(context, CustomWebActivity::class.java)
// Launch the intent
startActivity(customThanxWebViewIntent)
```

#### Implementing the callbacks with WebFragment

To add the loading callbacks to the WebFragment, your activity that launches
the fragments needs to implement the `LoadingInterface`:

**Java**:

```java
public class MainActivity extends AppCompatActivity implements LoadingListener {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    // ...

    // Display the fragment however you want
    getSupportFragmentManager()
      .beginTransaction()
      .add(R.id.web_fragment_container, WebFragment.newInstance(null), "thanx_web_fragment")
      .addToBackStack("thanx_web_fragment")
      .commit();
  }


  @Override
  public void onInitialLoadFinish(@Nullable String url) {
    // Callback triggered when the initial load starts
    // E.g. show loading spinner
  }

  @Override
  public void onInitialLoadStart(@Nullable String url) {
    // Callback triggered when the initial load finishes
    // E.g. hide loading spinner
  }

  @Override
  public void onLoadFinish(@Nullable String url) {
    // Callback triggered when any load starts (including the initial load)
  }

  @Override
  public void onLoadStart(@Nullable String url) {
    // Callback triggered when any load finishes (including the initial load)
  }
}
```

**Kotlin**:

```kotlin
class MainActivity : AppCompatActivity(), LoadingListener {

  override fun onCreate(savedInstanceState: Bundle?) {
    // ...

    // Instantiate a new WebFragment
    val webFragment = WebFragment.newInstance(null);

    // Display the fragment however you want
    getSupportFragmentManager()
      .beginTransaction()
      .add(R.id.web_view, webFrament, "thanx_web_fragment")
      .addToBackStack("thanx_web_fragment")
      .commit();

    // ...
  }

  override fun onInitialLoadStart(url: String?) {
    // Callback triggered when the initial load starts
    // E.g. show loading spinner
  }

  override fun onInitialLoadFinish(url: String?) {
    // Callback triggered when the initial load finishes
    // E.g. hide loading spinner
  }

  override fun onLoadFinish(url: String?) {
    // Callback triggered when any load starts (including the initial load)
  }

  override fun onLoadStart(url: String?) {
    // Callback triggered when any load finishes (including the initial load)
  }
}
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

## Play Store Rating Prompt
The default app rating prompt will display automatically after using a reward (or activating it if is a statement credit reward).

### Custom text
To customize the text that gets displayed in the rate prompt, you can add the following keys to your `strings.xml` file

```
<string name="rate_prompt_title">Custom title text</string>
<string name="rate_prompt_body">Custom body text</string>
<string name="rate_prompt_positive_button">Custom positive button text</string>
<string name="rate_prompt_negative_button">Custom negative button text</string>
```

### Disable
In order to disable automatically displaying the prompt, add the following line after SDK initialization:

**Java**:

```java
# SDK initialization
Thanx.Companion.initialize(this, "token", false, true, null);
# Disable Play Store rate prompt after reward redemption
Thanx.displayRatePrompt(false);
```

**Kotlin**:

```kotlin
# SDK initialization
Thanx.initialize(context = this, accessToken = "token", debug = false, logging = true)
# Disable Play Store rate prompt after reward redemption
Thanx.displayRatePrompt(false)
```


## Debug Mode

In order to run the SDK in debug mode, you'll need to initialize it with the debug flag as true:

**Java**:

```java
// MainActivity.java
Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true);
Thanx.initialize(this, "USER_ACCESS_TOKEN", true);
```

**Kotlin**:

```kotlin
MainActivity.kt
Thanx.initialize(this, "YOUR_CLIENT_ID", "YOUR_CLIENT_SECRET", true)
Thanx.initialize(this, "USER_ACCESS_TOKEN", true)
```

Running the SDK in debug will provide you with extra console output and most
importantly, it will point to the **Thanx Staging environment**.

**You should always run in Debug mode unless is a production build.**
