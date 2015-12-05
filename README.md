# Config variables for React Native apps in Android

Module to expose variables set in Gradle to your javascript code in React Native.


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

          // initialize our PhotoRequest handler
          mPhotoRequest = new ReactPhotoRequestPackage(this);

          mReactInstanceManager = ReactInstanceManager.builder()
                  .setApplication(getApplication())
                  .setBundleAssetName("index.android.bundle")
                  .setJSMainModuleName("index.android")
                  .addPackage(new MainReactPackage())
                  .addPackage(new ReactConfigPackage()) // add the package here
  ```
