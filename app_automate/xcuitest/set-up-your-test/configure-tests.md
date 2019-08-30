# Configure XCUI Tests
XCUITest can be executed using the following curl command.

curl -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/build" -d "{\"devices\": [\"iPhone 8 Plus-11\"], \"app\": \"bs://<hashed appid>\", \"deviceLogs\" : \"true\", \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESSKEY"

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
The test results are available on the command-line interface, as well as the [App Automate dashboard](https://app-automate.browserstack.com/dashboard).
