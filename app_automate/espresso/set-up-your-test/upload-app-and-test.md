# Upload your Android App and Espresso Test

Upload your Android app (.apk file) to the BrowserStack servers using the REST API.

>Note: Note: If you do not have an .apk file and are looking to simply try App Automate, you can download our [Calculator app](https://www.browserstack.com/app-automate/sample-apps/android/Calculator.apk) and upload to the BrowserStack servers using the above API. Complete list of REST APIs can be found [here](https://www.browserstack.com/app-automate/rest-api?framework=espresso).

```
curl -u "USERNAME:ACCESS_KEY" \
-X POST "https://api-cloud.browserstack.com/app-automate/upload" \
-F "file=@/path/to/app/file/Application-debug.apk"
```

Please note the App URL (bs://<hashed appid>) returned in the response of this call:

```
{"app_url":"bs://<hashed appid>"}

```
> Note:
1. App upload will take few seconds to about a minute depending on the size of your app. Do not interrupt the curl command until you get the response back.
2. If you upload an iOS app, we will resign the app with our own provisioning profile to be able to install your app on our devices during test execution.
3. We will delete the uploaded app after 30 days from the date of upload.

### Upload app from Public Location
If you do not have your app file on the local machine from where you are running the test and it is hosted on a different location, you can upload it to the BrowserStack servers from any public hosted location. You can achieve that by just providing the app public url in the API call to upload an app. The url should be accessible over the internet so that BrowserStack can directly upload it from that location. Use the below API call to upload app from a publicly accessible location.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/upload" -F "data={\"url\": \"https://www.browserstack.com/app-automate/sample-apps/android/Calculator.apk\"}"
```

### Define Custom Id for your app
You can choose to set a custom_id for your apps. You can use this custom_id while executing your test in step 3. Every time you upload an app with the same custom id, the test execution will pick the last uploaded app for the custom_id you used. Custom Id is optional.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/upload" -F "file=@/path/to/app/file/Application-debug.apk" -F "data={\"custom_id\": \"MyApp\"}"
```

Sample response:

```
{"custom_id":"MyApp", "app_url":"bs://<hashed appID>", "shareable_id":"USERNAME/MyApp"}
```

You can use either of the above values in the 'app' parameter while executing a test.


### Upload Espresso Test suite
Upload your Espresso test suite (.apk file) to BrowserStack servers using the REST API. Please note the Test ID returned in the response of this call.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/espresso/test-suite" -F "file=@/path/to/test/file/Application-debug-test.apk"
```
Sample response:

```
{"test_url":"bs://<hashed testID>"}
```

The Test ID returned in the response will be used to execute your test.

>Note: Note: Refer to our sample test repo on github: [espresso-app-browserstack](https://github.com/browserstack/espresso-browserstack) for the calculator sample app or download the test apk file: [CalculatorTest.apk](https://www.browserstack.com/app-automate/sample-apps/android/CalculatorTest.apk)

>Note: Test upload will take few seconds to about a minute depending on the size of your test suite. Do not interrupt the curl command until you get the response back.

### Upload test app from a Public Location

If you do not have your test app file on the local machine from where you are running the test and it is hosted on a different location, you can upload it to the BrowserStack servers from any public hosted location. You can achieve that by just providing the test app public url in the API call to upload an test app. The url should be accessible over the internet so that BrowserStack can directly upload it from that location. Use the below API call to upload test app from a publicly accessible location.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/espresso/test-suite" -F "data={\"url\": \"https://www.browserstack.com/app-automate/sample-apps/android/CalculatorTest.apk\"}"

```
### Define Custom ID for your test:

You can choose to set a custom_id for your test suite. You can use this custom_id while executing your test in step 3. Every time you upload a test with the same custom test id, the test execution will pick the last uploaded test suite for the custom_id you used. Custom ID is optional.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/espresso/test-suite" -F "file=@/path/to/app/file/Application-debug-test.apk" -F "data={\"custom_id\": \"MyTest\"}"
```
Sample response:
```
{"test_url":"bs://<hashed appid>", "custom_id":"MyTest", "shareable_id":"USERNAME/MyTest"}
```

You can use either of the above values in the 'testsuite' parameter while executing a test.
