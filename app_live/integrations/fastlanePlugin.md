# fastlane plugin to upload apps to App Live

Upload Android and iOS apps to App Live using fastlane plugin. Interactively test your native or hybrid apps on our Real Device Cloud.

### Introduction

[fastlane](https://fastlane.tools/) is a tool to automate beta deployments and releases for iOS and Android apps. It handles tasks like generating screenshots, dealing with code signing, and releasing application etc. Add BrowserStack to the mix, and your mobile apps can be tested across 1100+ real devices. BrowserStack lets you integrate your Appium tests with fastlane using a plugin that will allow you to upload your app to the BrowserStack servers and test on the BrowserStack real device cloud.

### Getting Started

Easily upload your app to App Live using [BrowserStack fastlane Plugin](https://rubygems.org/gems/fastlane-plugin-browserstack) by following steps shared below.

**Install plugin**

Add BrowserStack's fastlane plugin

```
fastlane add_plugin browserstack
```

**Configure Fastfile**

Configure the plugin by adding below action in desired lane(s) of your Fastfile.

```
upload_to_browserstack_app_live(
  browserstack_username: ENV["BROWSERSTACK_USERNAME"],
  browserstack_access_key: ENV["BROWSERSTACK_ACCESS_KEY"],
  file_path: "browserstack_android_sample/build/outputs/apk/debug/browserstack_android_sample-debug.apk"
)
```

> Note:file_path parameter is not required if the app was built in the same lane with the help of Gradle or Gym plugin.

**Execute Lane**

Execute the lane to upload app to App Live servers.
```
fastlane <name_of_lane>
```

Uploaded apps are automatically shared with team members, allowing you to test collaboratively.

**Sample Project**

To view sample project for reference, [click here](https://github.com/browserstack/browserstack-android-sample-app).

