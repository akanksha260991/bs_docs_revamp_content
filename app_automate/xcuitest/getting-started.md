# Getting Started with XCUITest

#### Documentation for running XCUITest automated mobile app tests with BrowserStack.

***
BrowserStack supports XCUITest framework for iOS mobile app testing, and running your tests on our cloud setup is simple and straightforward. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel

***

### Step 1: Run your first test

> Note: Running your XCUITest tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Follow 4 easy steps to get started with your first XCUITest test on BrowserStack cloud.

#### 1. Upload your app
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

Different ways you can upload your app can be found [here](/docs/app-automate/xcuitest/upload-app-and-test).

#### 2. Upload your test
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

```curl -u "kalpeshdoshi3:To3qiyBuMysSfYVPdeBz" -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/test-suite" -F "file=@/path/to/test/file/ApplicationUITests-Runner.zip"
```

Sample response:
```
{"test_url":"bs://<hashed testID>"}
```

The Test ID returned in the response will be used to execute your test.

>Note: If you do not have an XCUITest suite and looking to simply try XCUITest on App Automate, you can download our [Sample XCUITest App](https://www.browserstack.com/app-automate/sample-apps/ios/BrowserStack-SampleXCUITest.zip) and upload to the BrowserStack servers using the above API. Refer to our sample test repo - [xcuitest-sample-browserstack](https://github.com/browserstack/xcuitest-sample-browserstack)

>Note: Test upload will take few seconds to about a minute depending on the size of your test suite. Do not interrupt the curl command until you get the response back.

Different ways you can upload your app can be found [here](/docs/app-automate/xcuitest/upload-app-and-test).

#### 3. Execute XCUITest
Execute your test using the below API

Select a device (Add device selector)

```
curl -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/build" -d "{\"devices\": [\"iPhone 8 Plus-11\"], \"app\": \"bs://<hashed appid>\", \"deviceLogs\" : \"true\", \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS_KEY"
```

Complete list of API parameters:


| Parameters | Required/Optional | Descriptiojn |
| ---------- | ----------- | --------------- |
|`app`| Required | app_url of the app you want to test |
|`devices`| Required | list of Device and OS version you want to run your tests on. Acceptable format is -. Example: iPhone 8 Plus-11 |
|`testsuite`| Required | test_url of the test suite you want to test |
|`debugscreenshots`| Optional | Set this parameter to true to save the screenshots automatically captured by XCode. Screenshots can be rendered by accessing the rawlogs using the [XCUITest sessions](https://www.browserstack.com/app-automate/rest-api?framework=xcuitest#sessions) API. |  
|`only-testing`| Optional | Set this parameter to test only some selected classes or tests. Acceptable format is: [\"SampleXCUITestsPackage/SampleXCUITestsClass\", \"SampleXCUITestsPackage/SampleXCUITestsClass/testAlertTest\"] |
|`skip-testing`| Optional | Set this parameter if you want to skip particular classes or tests from testing. Acceptable format is: [\"SampleXCUITestsPackage/SampleXCUITestsClass\", \"SampleXCUITestsPackage/SampleXCUITestsClass/testAlertTest\"] |
|`setEnvVariables`| Optional | Use this parameter in order to pass some key and value pairs to be set in the environment variable which your test can read and pass it to your app as a launch argument. Example: \"setEnvVariables\" : {\"key1\":\"value1\", \"key2\":\"value2\"}. Acceptable character limit for the key is 30 and for the value is 100 characters |
|`deviceLogs`|Optional|Set this parameter if you want to enable the device logs|
|`video`|Optional|Set this parameter if you want to enable the video of the test run. By default it's set to `true`|
|`local`|Optional|Set this parameter if you want to enable local testing. By default its set to `false`|
|`localIdentifier`|Optional|If you are using same account to test multiple applications, you can setup named connection using the localIdentifier option. Example: \"localIdentifier\": \"Test123\"|
|`callbackURL`|Optional|Set the callback url where we can send a confirmation once your test execution is completed|
|`networkLogs`|Optional|Set this parameter if you want to enable the Network Logs. By default its `false`.|
|`networkProfile`|Optional|Required if you want to simulate different network conditions from the list of existing network profiles. Example: \"networkProfile\" : \"2g-gprs-good\". Complete list of network profiles can be found [here](https://www.browserstack.com/app-automate/network-simulation).|
|`customNetwork`|Optional|Required if you want to simulate the custom network conditions. Accepted values: download speed (kbps), upload speed (kbps), latency (ms), packet loss (%). Example: \"customNetwork\" : \"1000,1000,100,1\"|
|`otherApps`|Optional|Set this capability if you want to install apps in addition to the main app. Example: \"otherApps\" : [\"bs://\"]. Maximum 3 app ids allowed. This parameter has to be used along with app parameter.|
|`acceptInsecureCerts`|Optional|Set this parameter to true to avoid invalid certificate errors while using self-signed certificate to test your app. By default its set to `false`|
|`customBuildName`|Optional|Set this parameter if you would like to group your test executions under one build. Example: \"customBuildName\": \"Build123\"|
|`customBuildNotifyURL`|Optional|Set the the notify url for Custom Builds where we can send a confirmation once execution of all the builds under the customBuildName are completed. The callback will be sent if there are no builds triggered for 5 mins with the same customBuildName post previous execution. Set this parameter along with customBuildName parameter.|
|`geoLocation`|Optional|Required if you want to test how your app behaves in specific countries. "CN" for China, "FR" for France, "IN" for India and "US" for United States of America. Complete list of of over 45+ countries can be found [here](https://www.browserstack.com/ip-geolocation)|
|`gpsLocation`|Optional|Required if you want to simulate the location of the device to a particular GPS location. Acceptable range for latitude is -90 to +90 and for longitude is -180 to +180. Example: \"gpsLocation\": \"40.730610,-73.935242\"|
|`uploadMedia`|Optional|Set this parameter if you want to use your uploaded images or videos in the test. Upload your custom media on BrowserStack servers using REST API. Use the hashed url returned as a result of the upload in this capability. Example: ["media://hashedid", "media://hashedid"]<br/><br/> *Only 5 files are allowed per test <br/> *Supported format for images are JPG, JPEG, PNG, GIF, BMP <br/> *Max file size allowed for images is 10MB <br/> *Supported format for videos are: .mp4, .mov and .3gp <br/> *Max file size allowed for videos is 50MB|
|`language`|Optional|Set this parameter if you want to change the language to test localization of your App. Example: \"language\": \"fr\"|
|`locale`|Optional|Set this parameter if you want to change the locale to test localization of your App. Example: \"locale\": \"fr\"|
|`deviceOrientation`|Optional|Set this parameter to change screen orientation of mobile device. Acceptable values are portrait & landscape. By default the orientation is portrait.|

Sample response:
```
{"message"=>"Success", "build_id"=>"<Build ID>"}
```
The test results are available on the command-line interface, as well as the [App Automate dashboard](https://app-automate.browserstack.com/dashboard). You have now run your first Espresso test on BrowserStack App Automate.

#### 4. View Test Results
Once you run an automated test, you will be able to view the test result on the App Automate dashboard. To access the Dashboard, login to browserstack.com and click on the "App Automate" tab in the header. On the Dashboard, Your every test execution is one build. Every time you execute a test we will associate a build number (as #1, #2 etc) for you to identify your test execution. There will also be a unique build id to identify a unique test suite execution.

![App Build](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/app-automate/get-started-xcui-build-2x.png)

Dashboard also allows you to see the detail of a specific test session. Click on the session id in right column to see session details. In session detail view you will have access to all the session information, and debugging capabilities like, video of the session run, Instrumentation logs and Device logs.

![App Session](https://dgzoq9b5asjg1.cloudfront.net/production/images/static/docs/app-automate/get-started-xcui-session-2x.png)


### Step 2: Test on internal networks
Refer to [Local Testing documentation](https://www.browserstack.com/docs/app-automate/xcuitest/getting-started-with-local-testing) to learn about running a Local Test on BrowserStack

### Step 3: Run tests in Parallel
Parallel testing is running your XCUITest test suite on multiple devices concurrently to reduce your testing time, and is a key feature of BrowserStack App Automate. With access to our extensive BrowserStack cloud, parallel testing on many platforms becomes very efficient.
