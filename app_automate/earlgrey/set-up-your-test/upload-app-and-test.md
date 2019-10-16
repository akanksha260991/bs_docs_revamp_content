# Upload your .app directory

EarlGrey tests are the unit tests based on XCTest framework, you just need to upload your .app directory which will contain your app as well as the test suite for the app.

Follow below steps to create a zip of your .app directory and upload using the REST API.

If you are using XCode to build your app and write EarlGrey tests:

1. From the schemes dropdown, select the app scheme, and device as "Generic iOS device"

2. Product → Clean (Cmd Shift K)

3. Product → Build For → Testing (Cmd Shift U)

4. Use Xcode's Project Navigator to right click on .app and navigate to "Show in Finder" option

![](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/xcui-xcode-01-2x.png)5. 

5. Create a zip of the .app directory and upload using the below REST API

```
curl -u "USERNAME:ACCESS-KEY" -X POST "https://api-cloud.browserstack.com/app-automate/earlgrey/app-dir" -F "file=@/path/to/app/file/appDir.zip"
```

Sample response:

```
{"test_url":"bs://<hashed testID>"}
```

The Test ID returned in the response will be used to execute your test.

> **Note:** If you do not have an EarlGrey suite and looking to simply try EarlGrey on App Automate, you can download our [Sample EarlGrey App](https://www.browserstack.com/app-automate/sample-apps/ios/BStack-EarlGrey-SampleApp.zip) and upload to the BrowserStack servers using the above API. Refer to our sample test repo - [earlgrey-sample-browserstack](https://github.com/browserstack/earlgrey-sample-browserstack)

> **Note:** The upload will take few seconds to about a minute depending on the size of your app directory. Do not interrupt the curl command until you get the response back.


#### Upload zip from a Public Location

If you do not have your zip file on the local machine from where you are running the test and it is hosted on a different location, you can upload it to the BrowserStack servers from any public hosted location. You can achieve that by just providing the zip public url in the API call to upload an zip. The url should be accessible over the internet so that BrowserStack can directly upload it from that location. Use the below API call to upload zip from a publicly accessible location.

```
curl -u "USERNAME:ACCESS-KEY" -X POST "https://api-cloud.browserstack.com/app-automate/earlgrey/app-dir" -F "data={\"url\": \"https://www.browserstack.com/app-automate/sample-apps/ios/BStack-EarlGrey-SampleApp.zip\"}"
```


#### Define Custom ID for your test:

You can choose to set a custom_id for your test suite. You can use this custom_id while executing the test. Every time you upload a test with the same custom test id, the test execution will pick the last uploaded test suite for the custom_id you used. Custom ID is optional.

```
curl -u "USERNAME:ACCESSKEY" -X POST "https://api-cloud.browserstack.com/app-automate/earlgrey/app-dir" -F "file=@/path/to/app/file/appDir.zip" -F "data={\"custom_id\": \"MyTest\"}"
```

Sample response:
```
{"test_url":"bs://<hashed appID>", "custom_id":"MyTest", "shareable_id":"USERNAME/MyTest"}
```
You can use either of the above values for the test suite while executing a test.
