# Config variables for React Native apps in Android

Module to expose config variables set in Gradle to your javascript code in React Native.

Bringing some [12 factor](http://12factor.net/config) love to your mobile apps.


## Usage

Declare config variables in Gradle, under `android/app/build.gradle`:

```
android {
    defaultConfig {
      buildConfigField "String",  "API_URL",     '"https://myapi.com"'
      buildConfigField "Boolean", "SHOW_ERRORS", "true"
      ...
```

Then access those from javascript:

```js
var Config = require('react-native-android-config');

Config.API_URL     // "https://myapi.com"
Config.SHOW_ERRORS // true
```


## More on Gradle, config variables and 12 factor

In case you're wondering how to keep secrets outside your source: create `android/config.properties`:

```
API_URL="https://:secret@myapi.com"
```

Then load it from `build.gradle` like:

```
defaultConfig {
    def props = new Properties()
    props.load(new FileInputStream(rootProject.file('config.properties')))
    props.each { key, val -> buildConfigField "String", key, val }
}
```

You can do something similar under release in `buildTypes` to read a different set of credentials from another file.

A similar pattern can be used to cover variables in `AndroidManifest.xml`. For instance, if you need to declare your Google Maps API key:

```xml
<meta-data
  android:name="com.google.android.geo.API_KEY"
  android:value="@string/GOOGLE_MAPS_API_KEY" />
```

Then set that variable in `build.gradle`:

```
defaultConfig {
    resValue "string", "GOOGLE_MAPS_API_KEY", "..."
```

Or read it from a different file again, like:

```
defaultConfig {
    def props = new Properties()
    props.load(new FileInputStream(rootProject.file('config.properties')))
    props.each { key, val ->
        resValue "string", key, val
        buildConfigField "String", key, val
    }
```


## Setup

1. Include this module in `android/settings.gradle`:
  
  ```
  include ':react-native-android-config'
  include ':app'

  project(':react-native-android-config').projectDir = new File(rootProject.projectDir,
    '../node_modules/react-native-android-config/android')
  ```
2. Add a dependency to your app build in `android/app/build.gradle`:
  
  ```
  dependencies {
      ...
      compile project(':react-native-android-config')
  }
  ```
3. Change your main activity to add a new package, in `android/app/src/main/.../MainActivity.java`:
  
  ```java
  import com.lugg.ReactConfig.ReactConfigPackage; // add import

  public class MainActivity extends Activity implements DefaultHardwareBackBtnHandler {

      private ReactInstanceManager mReactInstanceManager;
      private ReactRootView mReactRootView;

      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          mReactRootView = new ReactRootView(this);

          mReactInstanceManager = ReactInstanceManager.builder()
                  .setApplication(getApplication())
                  .setBundleAssetName("index.android.bundle")
                  .setJSMainModuleName("index.android")
                  .addPackage(new MainReactPackage())
                  .addPackage(new ReactConfigPackage()) // add the package here
  ```

