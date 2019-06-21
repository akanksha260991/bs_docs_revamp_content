# Appium with WebdriverIO

#### Your guide to running mobile app tests using Appium with WebdriverIO on BrowserStack real device cloud.
***
BrowserStack gives you instant access to 2000+ real devices. Running your Appium test automation with WebdriverIO for native and hybrid mobile apps on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

***

### Step 1: Run your first test

> Note: Running your Appium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Follow 3 easy steps to get started with your first Appium test on BrowserStack cloud.

#### 1. Setup environment
Ensure you have WebdriverIO libraries installed.
```
# Install using npm
npm install webdriverio
```

#### 2. Upload your app
Upload your Android app (.apk file) or iOS app (.ipa file) to the BrowserStack servers using the REST API.

>Note: Note: If you do not have an .apk or .ipa file and are looking to simply try App Automate, you can download our [Android sample app](https://www.browserstack.com/app-automate/sample-apps/android/WikipediaSample.apk) or [iOS sample app](https://www.browserstack.com/app-automate/sample-apps/ios/BStackSampleApp.ipa) and upload to the BrowserStack servers using the above API. Complete list of REST APIs can be found [here](https://www.browserstack.com//app-automate/rest-api).

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

Different ways you can upload your app can be found [here](https://www.browserstack.com/docs/app-automate/appium/nodejs-webdriverio/upload-app).

#### 3. Configure and run test
Copy sample code provided in WebdriverIO for Android and iOS. Update the desired capability "app" with the App URL returned from the above API call.

<Android/iOS selector>
##### Android (Toggle)

If you are using our Sample App, the sample test below will install the Sample App (Wikipedia App) on the device, search for 'browserstack' and asserts for the list of results. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for WebdriverIO on Github: [webdriverio-appium-app-browserstack](https://github.com/browserstack/webdriverio-appium-app-browserstack)

```
var assert = require('assert');

describe('Search Wikipedia Functionality', function () {
  it('can find search results', function () {
    var searchSelector = `~Search Wikipedia`;
    browser.waitForVisible(searchSelector, 30000);
    browser
      .element(searchSelector)
      .click();

    var insertTextSelector = 'android=new UiSelector().resourceId("org.wikipedia.alpha:id/search_src_text")';
    browser.waitForVisible(insertTextSelector);
    browser
      .element(insertTextSelector)
      .keys('BrowserStack')
      .pause(5000);

    var allProductsName = browser.elements(`android.widget.TextView`).value;
    assert(allProductsName.length > 0);
  });
});
```
> Note:
> 1. UIAutomator2 is the default automation engine for Android.
> 2. Pass automationName as 'Appium' if you are using Appium automation engine
> 3. If your app uses webview, set the webview debugging flag to true when creating the webview object as described in the [Android remote debugging docs](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews): WebView.setWebContentsDebuggingEnabled(true);

##### iOS (Toggle)
If you are using our iOS Sample App, the sample test below will install the Sample App (WordPress App) on the device, navigate to the Login screen, enters the login email and check whether the email is registered on WordPress. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for WebdriverIO on Github: [webdriverio-appium-app-browserstack](https://github.com/browserstack/webdriverio-appium-app-browserstack)

```
var assert = require('assert');

describe('Text Verification', function () {
  it('should match displayed text with input text', function () {
    var textButton = `~Text Button`;
    browser.waitForVisible(textButton, 30000);
    browser
      .element(textButton)
      .click();

    var textInput = `~Text Input`;
    browser.waitForVisible(textInput, 30000);
    browser
      .element(textInput)
      .click()
      .keys("hello@browserstack.com"+"\n");

    var textOutput = `~Text Output`;
    browser.waitForVisible(textOutput, 30000);
    var value = browser.getText(textOutput)

    if (value === "hello@browserstack.com")
      assert(true)
    else
      assert(false)

  });
});
```

>Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.

To actually run the test case, we need to integrate with BrowserStack as follows.

##### Integration with BrowserStack
To integrate the sample WebdriverIO test case into BrowserStack, we have to update the .conf.js config files with the BrowserStack Hub URL and credentials required to connect to the BrowserStack Appium grid. The example below shows the single.conf.js config file, used for single test runs:

<Android / IOS Selector>
###### Android (Toggle)
```
exports.config = {
  user: 'USERNAME',
  key: 'ACCESS_KEY',

capabilities: [{
    name: 'single_appium_test',
    build: 'webdriver-browserstack',
    device: 'Google Pixel',
    app: process.env.BROWSERSTACK_APP_ID || 'bs://<hashed app-id>',
    'browserstack.debug': true
  }]
```

###### iOS (Toggle)
```
exports.config = {
  user: 'USERNAME',
  key: 'ACCESS_KEY',

 capabilities: [{
    name: 'single_appium_test',
    build: 'webdriver-browserstack',
    device: 'iPhone 7',
    app: process.env.BROWSERSTACK_APP_ID || 'bs://<hashed app-id>',
    'browserstack.debug': true
  }]
```

Run your test on BrowserStack using following command:

```
# Run using npm
npm run single
```

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

>Note:  In order to try a local test, upload our Local Sample App for [Android](https://www.browserstack.com/app-automate/sample-apps/android/LocalSample.apk) and [iOS](https://www.browserstack.com/app-automate/sample-apps/ios/LocalSample.ipa)

Configuring your WebdriverIO tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:

```
# Install using npm
npm install browserstack-local
```
2. Next, you need to update your WebdriverIO config file, and set the browserstack.local capability to true:

<Android / IOS Selector>
###### Android (Toggle)
```
var browserstack = require('browserstack-local');

exports.config = {
   user: 'USERNAME',
   key: 'ACCESS_KEY',

  capabilities: [{
    name: 'local_appium_test',
    build: 'webdriver-browserstack',
    device: 'Google Pixel',
    browserName: 'android',
    app: process.env.BROWSERSTACK_APP_ID || 'bs://<hashed app-id>',
    'browserstack.local': true,
    'browserstack.debug': true
  }],


  // Code to start browserstack local before start of test
  onPrepare: function (config, capabilities) {
    console.log("Connecting local");
    return new Promise(function(resolve, reject){
      exports.bs_local = new browserstack.Local();
      exports.bs_local.start({'key': exports.config.key }, function(error) {
        if (error) return reject(error);
        console.log('Connected. Now testing...');

        resolve();
      });
    });
  },

  // Code to stop browserstack local after end of test
  onComplete: function (capabilties, specs) {
    exports.bs_local.stop(function() {});
  }
};
```

Here is a sample test case written for running local with WebdriverIO.

```
describe('BrowserStack Local Testing', function () {
  it('can check tunnel working', function () {
    var searchSelector = 'android=new UiSelector().resourceId("com.example.android.basicnetworking:id/test_action")';
    browser.waitForVisible(searchSelector, 30000);
    browser
      .element(searchSelector)
      .click();

    var insertTextSelector = `android.widget.TextView`;
    browser.waitForVisible(insertTextSelector, 30000);

    var allTextElements = browser.elements(`android.widget.TextView`).value;
    browser.pause(10000);

    var testElement = null;
    allTextElements.forEach(function (textElement) {
      var textContent = browser.elementIdText(textElement['ELEMENT']).value;
      if (textContent.indexOf('The active connection is') !== -1) {
        testElement = textElement;
      }
    });

    if (testElement === null) {
      var screenshotPath = path.resolve(__dirname, 'screenshot.png');
      browser.saveScreenshot(screenshotPath);
      console.log('Screenshot stored at ' + screenshotPath);
      throw new Error('Cannot find the needed TextView element from app');
    }

    var matchedString = browser.elementIdText(testElement['ELEMENT']).value;
    console.log(matchedString);
    assert(matchedString.indexOf('The active connection is wifi') !== -1);
    assert(matchedString.indexOf('Up and running') !== -1);
  });
});
```
###### iOS (Toggle)
```
var browserstack = require('browserstack-local');

exports.config = {
   user: 'USERNAME',
   key: 'ACCESS_KEY',

capabilities: [{
    name: 'local_appium_test',
    build: 'webdriver-browserstack',
    device: 'iPhone 7',
    app: process.env.BROWSERSTACK_APP_ID || 'bs://<hashed app-id>',
    'browserstack.local': true,
    'browserstack.debug': true
  }],

// Code to start browserstack local before start of test
  onPrepare: function (config, capabilities) {
    console.log("Connecting local");
    return new Promise(function(resolve, reject){
      exports.bs_local = new browserstack.Local();
      exports.bs_local.start({'key': exports.config.key }, function(error) {
        if (error) return reject(error);
        console.log('Connected. Now testing...');

        resolve();
      });
    });
  },

  // Code to stop browserstack local after end of test
  onComplete: function (capabilties, specs) {
    exports.bs_local.stop(function() {});
  }
};
```
Here is a sample test case written for running local with WebdriverIO.
```
describe('BrowserStack Local Testing', function () {
  it('can check tunnel working', function () {
    var searchSelector = `~Test BrowserStackLocal connection`;
    browser.waitForVisible(searchSelector, 30000);
    browser
      .element(searchSelector)
      .click();

    var responseText = `~Response is: Up and running`;
    browser.waitForVisible(responseText, 30000);
    var allTextElements = browser.elements(responseText).value;

    var testElement = null;
    allTextElements.forEach(function (textElement) {
      var textContent = browser.elementIdText(textElement['ELEMENT']).value;
      if (textContent.indexOf('Up and running') !== -1) {
        testElement = textElement;
      }
    });

    if (testElement === null) {
      var screenshotPath = path.resolve(__dirname, 'screenshot.png');
      browser.saveScreenshot(screenshotPath);
      console.log('Screenshot stored at ' + screenshotPath);
      throw new Error('Cannot find the Up and running response');
    }

    var matchedString = browser.elementIdText(testElement['ELEMENT']).value;
    assert(matchedString == 'Response is: Up and running');
  });
});
```

3. You can now run your WebdriverIO test using BrowserStack Local with the following command:
```
# Run using npm
npm run local

```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple WebdriverIO tests at the same time across various device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with WebdriverIO on BrowserStack, modify the config file as shown below:

<Android / IOS Selector>
###### Android (Toggle)

```
exports.config = {
   user: 'USERNAME',
   key: 'ACCESS_KEY',

  capabilities: [{
    device: 'Google Pixel'
  }, {
    device: 'Samsung Galaxy S7'
  }, {
    device: 'Samsung Galaxy S6'
  }, {
    device: 'Google Nexus 9'
  }],


// Code to support common capabilities
exports.config.capabilities.forEach(function(caps){
  for(var i in exports.config.commonCapabilities) caps[i] = caps[i] || exports.config.commonCapabilities[i];
});
```

###### iOS (Toggle)

```
exports.config = {
   user: 'USERNAME',
   key: 'ACCESS_KEY',

  capabilities: [{
    "device": "iPhone 7"
  }, {
    "device": "iPhone 7 Plus"
  }],


// Code to support common capabilities
exports.config.capabilities.forEach(function(caps){
  for(var i in exports.config.commonCapabilities) caps[i] = caps[i] || exports.config.commonCapabilities[i];
});
```

You can now run your tests in parallel on BrowserStack using the following command:
```
# Run using paver
npm run parallel
```
>Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your WebdriverIO test cases.

```
var request = require("request");
request({uri: "https://USERNAME:ACCESS_KEY@api-cloud.browserstack.com/app-automate/sessions/<session-id>.json", method:"PUT", form:{"status":"completed","reason":""}})

```
The two potential values for status can either be passed or failed. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/app-automate/appium/api-reference).

### Step 4: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Appium command run through your WebdriverIO script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
capabilities: [{
  'browserstack.debug': true
}]
```
**Video Recording**
Every test run on the BrowserStack Appium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
capabilities: [{
  'browserstack.video': false
}]
```
In addition to these logs BrowserStack also provides Raw logs, Network logs and Appium logs. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/app-automate/appium/nodejs-webdriverio/debug-failed-tests)

### Next steps
Congrats! You are now able to run your tests on BrowserStack cloud. You may want to check out these resources:
***
#### [Testing on internal networks](https://www.browserstack.com/docs/app-automate/appium/<language>/getting-started-with-local-testing)
***
#### [Run tests in parallel](https://www.browserstack.com/docs/app-automate/appium/<language>/getting-started-with-parallel-testing)
***
#### [Debug your app](https://www.browserstack.com/docs/app-automate/appium/<language>/view-test-results)
***
#### [Integrate with CICD Tools](https://www.browserstack.com/docs/app-automate/appium/<language>/jenkins)
***
### Questions?
We're always happy to help with any questions you might have! Read [FAQ](https://www.browserstack.com/support?tag=app-automate), [Contact support](https://www.browserstack.com/contact), or [connect with our sales team](https://www.browserstack.com/contact#sales).

```
Was this page helpful? Yes No
```
