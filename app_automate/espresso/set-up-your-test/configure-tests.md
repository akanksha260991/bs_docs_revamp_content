# Configure Espresso Tests
Espresso test can be executed using the following curl command.

```
curl -X POST "https://api-cloud.browserstack.com/app-automate/espresso/build" -d \ "{\"devices\": [\"Samsung Galaxy S8-7.0\"], \"app\": \"bs://<hashed appid>\", \"deviceLogs\" : true, \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS_KEY"
```

Complete list of API parameters:


| Parameters | Required/Optional | Description |
| ---------- | ----------- | --------------- |
|`app`| Required | app_url of the app you want to test |
|`devices`| Required | list of Device and OS version you want to run your tests on. Acceptable format is -. Example: Google Pixel-8.0 |
|`testsuite`| Required | test_url of the test suite you want to test |
|`package`| Optional | Set this parameter to test only some selected packages. Acceptable format is: [\"com.browserstack\", \"com.browserstack1\"] |  
|`class`| Optional | Set this parameter to test only some selected classes. Acceptable format is: [\"com.browserstack.class1\", \"com.browserstack.class2\"] |
|`annotation`| Optional | Set this parameter if you want to run tests only for particular annotations [\"P1\", \"P2\"] |
|`size`| Optional | Set this parameter if you want to run tests that have @SmallTest, @MediumTest and @LargeTest annotations |
|`timezone`| Optional | Set this parameter if you want to configure tests to run on a custom time zone. Example: UTC, New_York (for America/New_York), Los_Angeles (for America/Los_Angeles), La_Rioja (for America/Argentina/La_Rioja). You can view the [complete list of timezones on Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |
|`appStoreConfiguration`| Optional | Set this parameter if you want to login to your google account on the devices in order to test the functionalities like Google Pay or Google In-app purchase. Example: { "username" : "play-store-email", "password" : "play-store-password" } |
|`enableSpoonFramework`| Optional | Set this parameter if you want to capture the screenshots of your tests using the Spoon framework. By default the value is set to false. See the [espresso-app-browserstack](https://github.com/browserstack/espresso-browserstack) sample app for more details on integrating Spoon framework. Render the screenshots for your build using the [Espresso sessions](https://www.browserstack.com/app-automate/rest-api?framework=espresso#sessions) API. |
|`disableAnimations`| Optional | Set this parameter to 'true' to disable animations on the device. |
|`allowDeviceMockServer`|Optional| Set this parameter to 'true' if you are using Mock server on devices to test your app. Note: Local, NetworkLogs, IP geoLocation will not work if you enable this capability. |
|`numShards` & `shardIndex`|Optional| Set these parameters if you want to break your test cases and run a particular shard of your choice. It helps you to optimize the total test execution time. The numShards specifies the number of shards in which you want to divide your tests and shardIndex specifies the particular shard to execute. Example: "numShards": "4", "shardIndex": "2". Both the capabilities are mandatory and should be non-negative, to allow the sharding of your test cases. The numShards capability value should be greater than zero and shardIndex should be anywhere between 0 to (numShards-1). Also, if you want to groups all your shards under one build, make use of the capability customBuildName for grouping. Example: "customBuildName": "Build123" |
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
