# Upload App API

App Live supports HTTP API to upload app via command line interface or automation scripts.

## Upload App

An API to upload the App you want to test on the BrowserStack servers for interactive app testing. Use it to upload via CLI or automation scripts. Uploaded apps are automatically shared with all team members, allowing you to test collaboratively.

```
curl -u "USERNAME:ACCESS_KEY" -X POST https://api-cloud.browserstack.com/app-live/upload -F "file=@/path/to/app/file/Application-debug.apk"
```


>Note
1. Supported file formats are .apk for Android apps and .ipa for iOS apps.

2. App upload will take few seconds to about a minute depending on the size of your app. Do not interrupt the curl command until you get the response back.
3. If you upload an iOS app, we will resign the app with our own provisioning profile to be able to install your app on our devices during test execution.
4. We will delete the uploaded app after 60 days from the date of upload.
5. Max file size supported is 500 mb.
