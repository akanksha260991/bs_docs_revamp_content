# Fastlane

Integrate your Appium test suite with Fastlane and the BrowserStack device cloud for native and hybrid apps using our Fastlane plugin.

# Introduction
[Fastlane](https://fastlane.tools/) is a tool to automate beta deployments and releases for iOS and Android apps. It handles tasks like generating screenshots, dealing with code signing, and releasing application etc. Add BrowserStack to the mix, and your mobile apps can be tested across 1100+ real devices. BrowserStack lets you integrate your Appium tests with Fastlane using a plugin that will allow you to upload your app to the BrowserStack servers and test on the BrowserStack real device cloud.


# Getting Started

### Install plugin:
Add BrowserStack's Fastlane plugin by executing the below command:

```bash
fastlane add_plugin browserstack
```

You can easily upload your app using BrowserStack Fastlane Plugin and execute your test on the BrowserStack cloud. In order to configure the plugin add below action in your Fastfile. You can also refer to the sample android project, which demonstrates the use of this plugin.

```bash
upload_to_browserstack_app_automate(
  browserstack_username: ENV["BROWSERSTACK_USERNAME"],
  browserstack_access_key: ENV["BROWSERSTACK_ACCESS_KEY"],
  file_path: "<path_to_your_apk_or_ipa_file>",
  custom_id: "<custom_id_name>"
)
```

> Note: custom_id is optional. You can upload multiple builds under the same custom_id. Use custom_id in 'app' capability for Appium to always pick the last uploaded build. The file_path parameter is not required if the app was built in the same lane with the help of Gradle or Gym plugin.

Once the app upload is successful, the app id of the app will be stored in an environment variable, "BROWSERSTACK_APP_ID" and it can be accessed in your tests in the following way :

```java
String app = System.getenv("BROWSERSTACK_APP_ID"); // Get the app id from environment variable
capabilities.setCapability("app", app); // Use the app id in the app capability in your test
```
