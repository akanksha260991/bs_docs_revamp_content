# Upload App from Public Url

If you do not have your app file on the local machine from where you are running the test and it is hosted on a different location, you can upload it to the BrowserStack servers from any public hosted location. You can achieve that by just providing the app public url in the API call to upload an app. The url should be accessible over the internet so that BrowserStack can directly upload it from that location. Use the below API call to upload app from a publicly accessible location.

```
curl -u "<username>:<accesskey>" \
-X POST "https://api-cloud.browserstack.com/app-automate/upload" \
-F "data={\"url\": \"https://www.browserstack.com/app-automate/sample-apps/android/WikipediaSample.apk\"}"
```

>Note: Please note the App URL (bs://<hashed appid>) returned in the response of this call:

```
{"app_url":"bs://<hashed appid>"}
```
> Note: If you do not have an .apk or .ipa file and are looking to simply try App Automate, you can download our [Android sample app](https://www.browserstack.com/app-automate/sample-apps/android/WikipediaSample.apk) or [iOS sample app](https://www.browserstack.com/app-automate/sample-apps/ios/BStackSampleApp.ipa) and upload to the BrowserStack servers using the above API.


--

> Note: 
1. App upload will take few seconds to about a minute depending on the size of your app. Do not interrupt the curl command until you get the response back.
2. If you upload an iOS app, we will resign the app with our own provisioning profile to be able to install your app on our devices during test execution.
3. We will delete the uploaded app after 30 days from the date of upload.
