# Gradle Plugin
### Documentation for running Espresso test using BrowserStack's Gradle Plugin.

## Getting Started
You can easily build your app and execute the test using [BrowserStack Gradle Plugin](https://github.com/browserstack/browserstack-gradle-plugin). In order to configure the plugin add below snippet in your app's build.gradle file.

Select a device (Add device selector)

```
plugins {
  id: com.browserstack.gradle
}

runDebugBuildOnBrowserstack {
  username = "UERNAME"
  accessKey = "ACCESSKEY"
  devices = ['Samsung Galaxy S8-7.0']
}
```

Execute below command to build your app and run your Espresso test

```
gradle runDebugBuildOnBrowserstack

```
This will execute the following:

1. Build debug and test apks, as dependencies are declared on assembleDebug and assembleDebugAndroidTest tasks.
2. Find the latest app apk and test apk in the app directory recursively.
3. Upload both the apks on BrowserStack server.
4. Execute Espresso test using the uploaded apps on the devices mentioned in the configuration.

### Complete list of supported parameters

| Parameters | Required/Optional | Description |
| ---------- | ----------- | --------------- |
|`username`| Required | Your BrowserStack username |
|`accessKey`| Required | Your BrowserStack accessKey |
|`devices`| Required | List of Device and OS version you want to run your tests on. Acceptable format is <devicename>-<os version>. Example: <br><br> `devices = ["Google Pixel-8.0", "Samsung Galaxy S8-7.1"]` |
|`deviceLogs`| Optional | Set this parameter if you want to enable the device logs |
|`packages`| Optional | Set this parameter to test only some selected packages. Acceptable format is: ["com.browserstack", "com.browserstack1"] |
|`classes`| Optional | Set this parameter to test only some selected classes. Acceptable format is: ["com.browserstack.class1", "com.browserstack.class2"] |
|`annotations`| Optional | Set this parameter if you want to run tests only for particular annotations ["P1", "P2"] |
|`sizes`| Optional | Set this parameter if you want to run tests that have @SmallTest, @MediumTest and @LargeTest annotations. Acceptable format is: ["small", "medium"] |
|`video`| Optional | Set this parameter if you want to enable the video of the test run. By default its set to true |
|`local`| Optional | Set this parameter if you want to enable local testing. By default its set to false |
|`localIdentifier`| Optional |  If you are using same account to test multiple applications, you can setup named connection using the localIdentifier option. Example: <br><br> `localIdentifier = "Test123"` |
|`callbackURL`| Optional | Set the callback url where we can send a confirmation once your test execution is completed |

The test results are available on the command-line interface, as well as the [App Automate dashboard](https://app-automate.browserstack.com/dashboard). You have now run your first Espresso test using Gradle Plugin on BrowserStack App Automate.
