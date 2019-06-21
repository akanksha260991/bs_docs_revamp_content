# Gradle Plugin

Documentation for uploading android app to App Live using BrowserStack's Gradle Plugin.

## Getting Started

You can easily integrate with App Live to build and upload app using [BrowserStack Gradle Plugin](https://github.com/browserstack/browserstack-gradle-plugin). Uploaded apps are automatically shared with all team members, allowing you to test collaboratively.

## Setting up

In order to configure the plugin, add below snippet in your app's build.gradle file.

```
plugins {
  id: com.browserstack.gradle
}

uploadBuildToBrowserstackAppLive {
  username = "BROWSERSTACK_USERNAME"
  accessKey = "BROWSERSTACK_ACCESS_KEY"
}
```

### Upload task

Execute below task to build and upload your app      .

```
gradle uploadBuildToBrowserstackAppLive
```

This will execute the following:

1. Build your apk as declared on assembleDebug.
2. Find the latest app apk in the app directory recursively.
3. Upload apk on BrowserStack server.

You have now uploaded the app to BrowserStack App Live using Gradle plugin.

