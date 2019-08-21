## Upload App from Local Machine 


Upload your Android app (.apk file) or iOS app (.ipa file) to the BrowserStack servers using the REST API.

```
curl -u "USERNAME:ACCESS_KEY" -X POST "https://api-cloud.browserstack.com/app-automate/upload" -F "file=@/path/to/app/file/Application-debug.apk" -F 'data={"custom_id": "MyApp"}'
```

>Note: custom_id is optional. You can upload multiple builds under same custom_id. Use custom_id in 'app' capability for Appium to always pick the last uploaded build.

You will get the response after the successfull app upload as:
>"app_url":"bs://<hashed appid>"}
  

This response (bs://<hashed appid) will be used to set the **app** capability in your tests.

> Note: If you do not have an .apk or .ipa file and are looking to simply try App Automate, you can download our [Android sample app](https://www.browserstack.com/app-automate/sample-apps/android/WikipediaSample.apk) or [iOS sample app](https://www.browserstack.com/app-automate/sample-apps/ios/BStackSampleApp.ipa) and upload to the BrowserStack servers using the above API.


--

> Note: 
1. App upload will take few seconds to about a minute depending on the size of your app. Do not interrupt the curl command until you get the response back.
2. If you upload an iOS app, we will resign the app with our own provisioning profile to be able to install your app on our devices during test execution.
3. We will delete the uploaded app after 30 days from the date of upload.

