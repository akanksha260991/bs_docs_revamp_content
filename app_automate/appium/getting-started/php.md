# Appium with PHPUnit

#### Your guide to running mobile app tests using Appium with PHPUnit on BrowserStack real device cloud.
***
BrowserStack gives you instant access to 2000+ real devices. Running your Appium test automation with PHPUnit for native and hybrid mobile apps on BrowserStack is simple. This guide will help you:

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
Ensure you have PHPUnit libraries installed.
```
# Install using composer
composer require phpunit/phpunit-selenium
composer require appium/php-client

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

Different ways you can upload your app can be found [here](https://www.browserstack.com/docs/app-automate/appium/c-sharp-phpunit/upload-app).

#### 3. Configure and run test
Copy sample code provided in PHPUnit for Android and iOS. Update the desired capability "app" with the App URL returned from the above API call.

<Android/iOS selector>
##### Android (Toggle)

If you are using our Sample App, the sample test below will install the Sample App (Wikipedia App) on the device, search for 'browserstack' and asserts for the list of results. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for PHPUnit on Github: [phpunit-appium-app-browserstack](https://github.com/browserstack/phpunit-appium-app-browserstack)

```
class AppAutomateTest extends BrowserStackTest {

    public function testNativeApplication() {
        $el = $this->byAccessibilityId('Search Wikipedia');
        $el->click();
        $this->keys("BrowserStack");
        sleep(2);
        $textElements = $this->elements($this->using('class name')->value('android.widget.TextView'));
        $this->assertTrue(sizeof($textElements) > 0);
        $this->assertInstanceOf('PHPUnit_Extensions_AppiumTestCase_Element', $el);
    }
}
```
> Note:
> 1. UIAutomator2 is the default automation engine for Android.
> 2. Pass automationName as 'Appium' if you are using Appium automation engine
> 3. If your app uses webview, set the webview debugging flag to true when creating the webview object as described in the [Android remote debugging docs](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews): WebView.setWebContentsDebuggingEnabled(true);

##### iOS (Toggle)
If you are using our iOS Sample App, the sample test below will install the Sample App (WordPress App) on the device, navigate to the Login screen, enters the login email and check whether the email is registered on WordPress. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for PHPUnit on Github: [phpunit-appium-app-browserstack](https://github.com/browserstack/phpunit-appium-app-browserstack)

```
class AppAutomateTest extends BrowserStackTest {

    public function testNativeApplication() {
        $el = $this->byAccessibilityId('Text Button');
        $el->click();
        sleep(2);
        $el2 = $this->byAccessibilityId('Text Input');
        $el2->click();
        sleep(2);
        $this->keys("hello@browserstack.com\n");
        sleep(5);
        $el3 = $this->byAccessibilityId('Text Output');
        printf("\n%s\n", $el3->text());
        $this->assertInstanceOf('PHPUnit_Extensions_AppiumTestCase_Element', $el);
        $this->assertTrue(strpos($el3->text(), "browserstack.com") != FALSE);
    }
}
```

>Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.

To actually run the test case, we need to integrate with BrowserStack as follows.

##### Integration with BrowserStack
Integration of PHPUnit with BrowserStack is made possible by use of following module:

```
<?php

require 'vendor/autoload.php';
require 'lib/globals.php';
require_once('PHPUnit/Extensions/AppiumTestCase.php');
require_once('PHPUnit/Extensions/AppiumTestCase/Element.php');

class BrowserStackTest extends \PHPUnit_Extensions_AppiumTestCase
{
    protected static $bs_local;
    public static $browsers = array(array( 'browserName' => '', 'desiredCapabilities' => array()));
    public function setupSpecificBrowser($params)
    {
        $CONFIG = $GLOBALS['CONFIG'];
        $task_id = getenv('TASK_ID') ? getenv('TASK_ID') : 0;

        $host = $GLOBALS['BROWSERSTACK_USERNAME'] . ":" . $GLOBALS['BROWSERSTACK_ACCESS_KEY'] . "@" . $CONFIG['server'];
        $caps = $this->getDesiredCapabilities();
        if(array_key_exists("browserstack.local", $GLOBALS['CONFIG']['capabilities']) && $GLOBALS['CONFIG']['capabilities']["browserstack.local"])
        {
            $bs_local_args = array("key" => $GLOBALS['BROWSERSTACK_ACCESS_KEY']);
            self::$bs_local = new BrowserStack\Local();
            self::$bs_local->start($bs_local_args);
        }
        $this->setHost($host);
        $this->setPort(443);
        $this->setSecure(TRUE);
        $caps = isset($params['desiredCapabilities']) ? $params['desiredCapabilities'] : array();
        if (!$local && !$host && isset($params['sessionStrategy'])) {
            $params['sessionStrategy'] = 'isolated';
        }
        $this->setDesiredCapabilities($caps);
        $this->setUpSessionStrategy($params);
    }

    public static function tearDownAfterClass()
    {
        if(self::$bs_local) self::$bs_local->stop();
    }
}
foreach($GLOBALS['CONFIG']['capabilities'] as $key => $value) {
    BrowserStackTest::$browsers[0]['desiredCapabilities'][$key] = $value;
}
foreach($GLOBALS['CONFIG']['devices'] as $device) {
    BrowserStackTest::$browsers[0]['desiredCapabilities']['device'] = $device["device"];
}
?>
```

The module reads from config file where you need to put the BrowserStack Hub URL and credentials. If the BROWSERSTACK_USERNAME and BROWSERSTACK_ACCESS_KEY are set in Environment variables, then that will take a preference. Also, update the App URL after uploading App to the BrowserStack cloud.


<Android / IOS Selector>
###### Android (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": 'USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "project": "PHPUnit_AppAutomate",
    "build": "phpunit-app_automate-browserstack",
    "name": "android_single",
    "browserstack.debug": true,
    "app":"bs://<hashed appid>"
  },
  "devices": [{
    "device": "Samsung Galaxy S8"
  }]
}
```

###### iOS (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "project": "PHPUnit_AppAutomate",
    "build": "phpunit-app_automate-browserstack",
    "name": "ios_single",
    "browserstack.debug": true,
    "app":"bs://<hashed appid>"
  },
  "devices": [{
    "device": "iPhone 7"
  }]
}
```

Run your test on BrowserStack using following command:

```
# Run using composer

  # For Android
  composer android_single

  # For IOS
  composer ios_single
```

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

>Note:  In order to try a local test, upload our Local Sample App for [Android](https://www.browserstack.com/app-automate/sample-apps/android/LocalSample.apk) and [iOS](https://www.browserstack.com/app-automate/sample-apps/ios/LocalSample.ipa)

Configuring your PHPUnit tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:

```
# Install using composer
composer require browserstack/browserstack-local

```
2. Next, you need to update your PHPUnit config file, and set the browserstack.local capability to true:

<Android / IOS Selector>
###### Android (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "project": "PHPUnit_AppAutomate",
    "build": "phpunit-app_automate-browserstack",
    "name": "android_local",
    "browserstack.debug": true,
    "browserstack.local": true,
    "app":"bs://<hashed appid>"
  },
  "devices": [{
    "device": "Samsung Galaxy S8"
  }]
}

<?php
    //code to start browserstack local before start of test
    if(array_key_exists("browserstack.local", $GLOBALS['CONFIG']['capabilities']) && $GLOBALS['CONFIG']['capabilities']["browserstack.local"])
    {
        $bs_local_args = array("key" => $GLOBALS['BROWSERSTACK_ACCESS_KEY']);
        self::$bs_local = new BrowserStack\Local();
        self::$bs_local->start($bs_local_args);
    }

    // code to stop browserstack local after end of test
    public static function tearDownAfterClass()
    {
        if(self::$bs_local) self::$bs_local->stop();
    }
?>
```

Here is a sample test case written for running local with PHPUnit.

```
class AppAutomateTest extends BrowserStackTest {

    public function testNativeApplication() {
        $el = $this->byId('com.example.android.basicnetworking:id/test_action');
        $el->click();
        sleep(2);
        $textElements = $this->elements($this->using('class name')->value('android.widget.TextView'));
        $testElement = null;
        foreach($textElements as $element) {
          if(strpos($element->text(), 'The active connection is')) {
            $testElement = $element;
          }
        }
        if($testElement == null) {
          $screenshotPath = getcwd() . '/screenshot.png';
          $handle = fopen($screenshotPath, 'w');
          $data = $this->currentScreenshot();
          fwrite($handle, $data);
          fclose($handle);
          printf("Screenshot stored at %s\n", $screenshotPath);
          printf("Cannot find the needed text view\n");
        }
        else {
          $matchedString = $testElement->text();
          $this->assertTrue(strpos($matchedString, 'The active connection is wifi') != false);
          $this->assertTrue(strpos($matchedString, 'Up and running') != false);
        }
    }
}
```
###### iOS (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "project": "PHPUnit_AppAutomate",
    "build": "phpunit-app_automate-browserstack",
    "name": "android_local",
    "browserstack.debug": true,
    "browserstack.local": true,
    "app":"bs://<hashed appid>"
  },
  "devices": [{
    "device": "iPhone 7"
  }]
}

<?php
    //code to start browserstack local before start of test
    if(array_key_exists("browserstack.local", $GLOBALS['CONFIG']['capabilities']) && $GLOBALS['CONFIG']['capabilities']["browserstack.local"])
    {
        $bs_local_args = array("key" => $GLOBALS['BROWSERSTACK_ACCESS_KEY']);
        self::$bs_local = new BrowserStack\Local();
        self::$bs_local->start($bs_local_args);
    }

    // code to stop browserstack local after end of test
    public static function tearDownAfterClass()
    {
        if(self::$bs_local) self::$bs_local->stop();
    }
?>
```
Here is a sample test case written for running local with PHPUnit.
```
class AppAutomateTest extends BrowserStackTest {

    public function testNativeApplication() {
        printf("\n IN test function \n");
        $searchSelector = $this->byAccessibilityId('Test BrowserStackLocal connection');
        $searchSelector->click();
        sleep(2);
        $responseText = $this->byAccessibilityId('Response is: Up and running');
        if($responseText == null) {
            $screenshotPath = getcwd() . '/screenshot.png';
            $handle = fopen($screenshotPath, 'w');
            $data = $this->currentScreenshot();
            fwrite($handle, $data);
            fclose($handle);
            printf("Screenshot stored at %s\n", $screenshotPath);
            printf("Cannot find the Up and running response\n");
        }
        else {
              $matchedString = $responseText->text();
              $this->assertTrue(strpos($matchedString, 'Up and running') != false);
        }
    }
}
```

3. You can now run your PHPUnit test using BrowserStack Local with the following command:
```
# Run using composer

# for android
composer android_local

# for ios
composer ios_local

```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple PHPUnit tests at the same time across various device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with PHPUnit on BrowserStack, modify the config file as shown below:

<Android / IOS Selector>
###### Android (Toggle)

```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "project": "PHPUnit_AppAutomate",
    "build": "phpunit-app_automate-browserstack",
    "name": "android_parallel",
    "browserstack.debug": true,
    "app":"bs://<hashed appid>"
  },
  "devices": [{
    "device": "Samsung Galaxy S8",
    "device": "Samsung Galaxy S8 Plus"
  }]
}
```

###### iOS (Toggle)

```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "project": "PHPUnit_AppAutomate",
    "build": "phpunit-app_automate-browserstack",
    "name": "android_parallel",
    "browserstack.debug": true,
    "app":"bs://<hashed appid>"
  },
  "devices": [{
    "device": "iPhone 7",
    "device": "iPhone 7 Plus"
  }]
}
```
You need the following custom script to launch the tests in parallel.

```
<?php
$run_cmd = "composer android_parallel"; // for ios "composer ios_parallel"
$num_of_tests = 2;
$procs = array();
for($x = 0; $x < $num_of_tests; $x++) {
  putenv("TASK_ID=$x");
  $procs[$x] = popen($run_cmd, "r");
}
for($x = 0; $x < $num_of_tests; $x++) {
  while (!feof($procs[$x])) {
        print fgets($procs[$x], 4096);
  }
  pclose($procs[$x]);
}
?>
```

You can now run your tests in parallel on BrowserStack using the following command:
```
# Run using composer
composer parallel
```
>Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your PHPUnit test cases.

```
file_get_contents('https://USERNAME:ACCESS_KEY@api-cloud.browserstack.com/app-automate/sessions/<session-id>.json', false, stream_context_create(array('http'=>array('method'=>'PUT','header'=>'Content-type: application/json', 'content'=>'{"status":"passed","reason":""}'))));

```
The two potential values for status can either be passed or failed. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/app-automate/appium/api-reference).

### Step 4: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Appium command run through your PHPUnit script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
"capabilities": {
  "browserstack.debug": true
}
```
**Video Recording**
Every test run on the BrowserStack Appium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
"capabilities": {
  "browserstack.video": false
}
```
In addition to these logs BrowserStack also provides Raw logs, Network logs and Appium logs. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/app-automate/appium/php-phpunit/debug-failed-tests)

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
