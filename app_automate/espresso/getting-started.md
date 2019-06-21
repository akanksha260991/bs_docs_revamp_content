# Getting Started with Espresso

#### Documentation for running Espresso automated mobile app tests with BrowserStack.

***
BrowserStack supports Espresso automated mobile app tests using Java, and running your tests on our cloud setup is simple and straightforward. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel

***

### Step 1: Run your first test

> Note: Running your Espresso tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Follow 3 easy steps to get started with your first Espresso test on BrowserStack cloud.

#### 1. Upload your app
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

Different ways you can upload your app can be found [here](/docs/app-automate/espresso/upload-app-and-test).

#### 1. Upload your test
Upload your Espresso test suite (.apk file) to BrowserStack servers using the REST API. Please note the Test ID returned in the response of this call.

```
curl -u "USERNAME:ACCESS_KEY" -X POST "https://api-cloud.browserstack.com/app-automate/espresso/test-suite" -F "file=@/path/to/test/file/Application-debug-test.apk"
```
Sample response:

```
{"test_url":"bs://<hashed testID>"}
```

The Test ID returned in the response will be used to execute your test.

>Note: Refer to our sample test repo on github: [espresso-app-browserstack](https://github.com/browserstack/espresso-browserstack) for the calculator sample app or download the test apk file: CalculatorTest.apk
