# Upload your iOS App and XCUITest Suite

Upload your iOS app (.ipa file) to the BrowserStack servers using the REST API.

>Note: Note: If you do not have an .ipa file and are looking to simply try App Automate, you can download our [Sample app](https://www.browserstack.com/app-automate/sample-apps/ios/BrowserStack-SampleApp.ipa) and upload to the BrowserStack servers using the above API. Complete list of REST APIs can be found [here](https://www.browserstack.com/app-automate/rest-api?framework=xcuitest).

```
curl -u "USERNAME:ACCESS_KEY" \
-X POST "https://api-cloud.browserstack.com/app-automate/upload" \
-F "file=@/path/to/app/file/Application-debug.ipa"
```

Please note the App URL (bs://<hashed appid>) returned in the response of this call:

```
{"app_url":"bs://<hashed appid>"}

```
> Note:
1. App upload will take few seconds to about a minute depending on the size of your app. Do not interrupt the curl command until you get the response back.
2. If you upload an iOS app, we will resign the app with our own provisioning profile to be able to install your app on our devices during test execution.
3. We will delete the uploaded app after 30 days from the date of upload.

### Upload app from a Public Location

If you do not have your app file on the local machine from where you are running the test and it is hosted on a different location, you can upload it to the BrowserStack servers from any public hosted location. You can achieve that by just providing the app public url in the API call to upload an app. The url should be accessible over the internet so that BrowserStack can directly upload it from that location. Use the below API call to upload app from a publicly accessible location.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/upload" -F "data={\"url\": \"https://www.browserstack.com/app-automate/sample-apps/ios/BrowserStack-SampleApp.ipa\"}"

```

### Define Custom Id for your app:

You can choose to set a custom_id for your apps. You can use this custom_id while executing your test in step 3. Every time you upload an app with the same custom id, the test execution will pick the last uploaded app for the custom_id you used. Custom ID is optional.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/upload" -F "file=@/path/to/app/file/Application-debug.ipa" -F "data={\"custom_id\": \"MyApp\"}"
```

Sample response:
```
{"custom_id":"MyApp", "app_url":"bs://<hashed appID>", "shareable_id":"USERNAME/MyApp"}

```

### Upload Test suite
Upload your XCUITest suite (.zip file) to BrowserStack servers using the REST API.

Follow below steps to create a zip file for your test suite.

If you are using XCode to build your test package:

1. From the schemes dropdown, select the app scheme, and device as "Generic iOS device"
2. Product → Clean (Cmd Shift K)
3. Product → Build For → Testing (Cmd Shift U)
4. Use Xcode's Project Navigator to right click on .app and navigate to "Show in Finder" option

![Show in Finder](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/app-automate/xcui-xcode-01-2x.png)

5. In the finder, select the appropriate <AppnameUITests>-Runner.app and navigate to the compress option. This should generate <AppnameUITests>-Runner.zip file

![Show in Finder](https://d98b8t1nnulk5.cloudfront.net/production/images/static/docs/app-automate/xcui-xcode-02-2x.png)

Follow below steps in order to automate this process:

1. Build the project for testing. Change to the project directory and run the following command to build for testing
```
xcodebuild -scheme <scheme-name> build-for-testing
```
2. From the command line, change to the derivedData directory. To know the location of your derivedDatadirectory, visit Xcode → Preferences → Locations.

3. Then from the command line, run the following commands:

```
cd <app_name>-<random_characters> # You can get the exact name of the last updated directory by ls -lrt cd Build/Products cd Debug-iphoneos zip --symlinks -r <MyAppUITests>.zip <AppnameUITests>-Runner.app
```

Upload the test zip file created using any of the above methods to the BrowserStack servers using the REST API

```curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/test-suite" -F "file=@/path/to/test/file/ApplicationUITests-Runner.zip"
```

Sample response:
```
{"test_url":"bs://<hashed testID>"}
```

The Test ID returned in the response will be used to execute your test.

>Note: If you do not have an XCUITest suite and looking to simply try XCUITest on App Automate, you can download our [Sample XCUITest App](https://www.browserstack.com/app-automate/sample-apps/ios/BrowserStack-SampleXCUITest.zip) and upload to the BrowserStack servers using the above API. Refer to our sample test repo - [xcuitest-sample-browserstack](https://github.com/browserstack/xcuitest-sample-browserstack)

>Note: Test upload will take few seconds to about a minute depending on the size of your test suite. Do not interrupt the curl command until you get the response back.

### Upload zip from a Public Location

If you do not have your zip file on the local machine from where you are running the test and it is hosted on a different location, you can upload it to the BrowserStack servers from any public hosted location. You can achieve that by just providing the zip public url in the API call to upload an zip. The url should be accessible over the internet so that BrowserStack can directly upload it from that location. Use the below API call to upload zip from a publicly accessible location.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/test-suite" -F "data={\"url\": \"https://www.browserstack.com/app-automate/sample-apps/ios/BrowserStack-SampleXCUITest.zip\"}"

```
### Define Custom ID for your test:

You can choose to set a custom_id for your test suite. You can use this custom_id while executing your test in step 3. Every time you upload a test with the same custom test id, the test execution will pick the last uploaded test suite for the custom_id you used. Custom ID is optional.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/test-suite" -F "file=@/path/to/app/file/ApplicationUITests-Runner.zip" -F "data={\"custom_id\": \"MyTest\"}"
```

Sample response:
```
{"test_url":"bs://<hashed appid>", "custom_id":"MyTest", "shareable_id":"USERNAME/MyTest"}
```
You can use either of the above values for the test suite while executing a test.
