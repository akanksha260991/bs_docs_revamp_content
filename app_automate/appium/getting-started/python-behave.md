# Appium with Behave

#### Your guide to running mobile app tests using Appium with Behave on BrowserStack real device cloud.
***
BrowserStack gives you instant access to 2000+ real devices. Running your Appium test automation with Behave for native and hybrid mobile apps on BrowserStack is simple. This guide will help you:

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
Ensure you have Behave libraries installed.
```
# Install using pip
pip install behave
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

Different ways you can upload your app can be found [here](/docs/app-automate/appium/python-behave/upload-app).

#### 3. Configure and run test
Copy sample code provided in Behave for Android and iOS. Update the desired capability "app" with the App URL returned from the above API call.

<Android/iOS selector>
##### Android (Toggle)

If you are using our Sample App, the sample test below will install the Sample App (Wikipedia App) on the device, search for 'browserstack' and asserts for the list of results. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for Behave on Github: [behave-appium-app-browserstack](https://github.com/browserstack/behave-appium-app-browserstack)

```
#Wikipedia Feature
Feature: Wikipedia Search Functionality
    Scenario: can find search results
        Given I open the app and search for keyword "BrowserStack"
        Then Search results should appear
```
```
@given(u'I open the app and search for keyword "{keyword}"')
def step_impl(context, keyword):
    search_element = WebDriverWait(context.browser, 10).until(
        EC.presence_of_element_located((MobileBy.ACCESSIBILITY_ID, "Search Wikipedia"))
    )
    search_element.click()

    search_input = WebDriverWait(context.browser, 30).until(
        EC.element_to_be_clickable((MobileBy.ID, "org.wikipedia.alpha:id/search_src_text"))
    )
    search_input.send_keys(keyword)
    time.sleep(5)


@then(u'Search results should appear')
def step_impl(context):
    elems = context.browser.find_elements_by_class_name("android.widget.TextView")
    assert len(elems) > 0, "results not populated"
```
> Note:
> 1. UIAutomator2 is the default automation engine for Android.
> 2. Pass automationName as 'Appium' if you are using Appium automation engine
> 3. If your app uses webview, set the webview debugging flag to true when creating the webview object as described in the [Android remote debugging docs](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews): WebView.setWebContentsDebuggingEnabled(true);

##### iOS (Toggle)
If you are using our iOS Sample App, the sample test below will install the Sample App (WordPress App) on the device, navigate to the Login screen, enters the login email and check whether the email is registered on WordPress. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for Behave on Github: [behave-appium-app-browserstack](https://github.com/browserstack/behave-appium-app-browserstack)

```
# WordPress Feature
Feature:
    Scenario: Displayed Text should match Input Text
        Given I open the app and click on Text Button
        Then Type "hello@browserstack.com" and hit enter
        Then Verify displayed text matches input text
```
```
@given('I open the app and click on Text Button')
def step_impl(context):
    element = WebDriverWait(context.browser, 30).until(
        EC.presence_of_element_located((MobileBy.ACCESSIBILITY_ID, "Text Button"))
    )
    element.click()


@then(u'Type "{text}" and hit enter')
def step_impl(context, text):
    text_input = WebDriverWait(context.browser, 30).until(
        EC.element_to_be_clickable((MobileBy.ACCESSIBILITY_ID, "Text Input"))
    )
    text_input.send_keys(text+"\n")
    time.sleep(5)


@then(u'Verify displayed text matches input text')
def step_impl(context):
    text_output = WebDriverWait(context.browser, 30).until(
        EC.element_to_be_clickable((MobileBy.ACCESSIBILITY_ID, "Text Output"))
    )
    if text_output!=None and text_output.text=="hello@browserstack.com":
        assert True
    else:
        assert False
```

>Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.

To actually run the test case, we need to integrate with BrowserStack as follows.

##### Integration with BrowserStack
Integration of Behave with BrowserStack is made possible by use of following module:

```
from appium import webdriver
from browserstack.local import Local
import os, json

CONFIG_FILE = os.environ['CONFIG_FILE'] if 'CONFIG_FILE' in os.environ else 'config/single.json'
TASK_ID = int(os.environ['TASK_ID']) if 'TASK_ID' in os.environ else 0

with open(CONFIG_FILE) as data_file:
    CONFIG = json.load(data_file)

bs_local = None

BROWSERSTACK_USERNAME = os.environ['BROWSERSTACK_USERNAME'] if 'BROWSERSTACK_USERNAME' in os.environ else CONFIG['user']
BROWSERSTACK_ACCESS_KEY = os.environ['BROWSERSTACK_ACCESS_KEY'] if 'BROWSERSTACK_ACCESS_KEY' in os.environ else CONFIG['key']

def start_local():
    """Code to start browserstack local before start of test."""
    global bs_local
    bs_local = Local()
    bs_local_args = { "key": BROWSERSTACK_ACCESS_KEY, "forcelocal": "true" }
    bs_local.start(**bs_local_args)

def stop_local():
    """Code to stop browserstack local after end of test."""
    global bs_local
    if bs_local is not None:
        bs_local.stop()


def before_feature(context, feature):
    desired_capabilities = CONFIG['environments'][TASK_ID]

    for key in CONFIG["capabilities"]:
        if key not in desired_capabilities:
            desired_capabilities[key] = CONFIG["capabilities"][key]

    if 'BROWSERSTACK_APP_ID' in os.environ:
        desired_capabilities['app'] = os.environ['BROWSERSTACK_APP_ID']

    if "browserstack.local" in desired_capabilities and desired_capabilities["browserstack.local"]:
        start_local()

    context.browser = webdriver.Remote(
        desired_capabilities=desired_capabilities,
        command_executor="http://%s:%s@hub-cloud.browserstack.com/wd/hub" % (BROWSERSTACK_USERNAME, BROWSERSTACK_ACCESS_KEY)
    )

def after_feature(context, feature):
    context.browser.quit()
    stop_local()
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials. If the BROWSERSTACK_USERNAME and BROWSERSTACK_ACCESS_KEY are set in Environment variables, then that will take a preference. Also, update the App URL after uploading App to the BrowserStack cloud.

<Android / IOS Selector>
###### Android (Toggle)
```
{
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "behave-browserstack",
    "name": "single_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "Google Pixel"
  }]
}
```
###### iOS (Toggle)
```
{
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "behave-browserstack",
    "name": "single_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "iPhone 7"
  }]
}
```

Run your test on BrowserStack using following command:

```
# Run using paver
paver run single
```

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

>Note:  In order to try a local test, upload our Local Sample App for [Android](https://www.browserstack.com/app-automate/sample-apps/android/LocalSample.apk) and [iOS](https://www.browserstack.com/app-automate/sample-apps/ios/LocalSample.ipa)

Configuring your Behave tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:

```
# Install using pip
pip install browserstack-local
```
2. Next, you need to update your Behave config file, and set the browserstack.local capability to true:

<Android / IOS Selector>
###### Android (Toggle)
```
{
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "behave-browserstack",
    "name": "local_test",
    "browserstack.debug": true,
    "browserstack.local": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "Google Pixel"
  }]
}
```

Here is a sample test case written for running local with Behave.

```
# Local Feature
Feature: BrowserStack Local Testing
    Scenario: can check tunnel working
        Given I open app and click on button
        Then page contains "Up and running"
```
```
# Local Steps
@given(u'I open app and click on button')
def step_impl(context):
    element = WebDriverWait(context.browser, 30).until(
        EC.presence_of_element_located((MobileBy.ID, "com.example.android.basicnetworking:id/test_action"))
    )
    element.click()
    WebDriverWait(context.browser, 30).until(
        EC.presence_of_element_located((MobileBy.CLASS_NAME, "android.widget.TextView"))
    )

@then(u'page contains "{regex}"')
def step_impl(context, regex):
    test_element = None
    search_results = context.browser.find_elements_by_class_name("android.widget.TextView")
    for result in search_results:
        if result.text.__contains__("The active connection is"):
            test_element = result

    if test_element is None:
        raise Exception("Cannot find the needed TextView element from app")

    matched_string = test_element.text
    assert matched_string.__contains__("The active connection is wifi"), "Incorrect Text"
    assert matched_string.__contains__("Up and running"), "Incorrect Text"
```
###### iOS (Toggle)
```
{
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "behave-browserstack",
    "name": "local_test",
    "browserstack.debug": true,
    "browserstack.local": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "iPhone 7"
  }]
}
```
Here is a sample test case written for running local with Behave.
```
# Local Feature
Feature: BrowserStack Local Testing
    Scenario: can check tunnel working
        Given I open app and click on button
        Then page contains "Up and running"
```
```
# Local Steps
@given(u'I open app and click on button')
def step_impl(context):
    test_button = WebDriverWait(context.browser, 30).until(
        EC.element_to_be_clickable((MobileBy.ACCESSIBILITY_ID, "TestBrowserStackLocal"))
    )
    test_button.click()


@then(u'page contains "{regex}"')
def step_impl(context, regex):
    WebDriverWait(context.browser, 30).until(existence_lambda)
    result_element = context.browser.find_element_by_accessibility_id("ResultBrowserStackLocal")
    result_string = result_element.text.lower()

    if result_string.__contains__("not working"):
        screenshot_file = "%s/screenshot.png" % os.getcwd()
        driver.save_screenshot(screenshot_file)
        print("Screenshot stored at %s" % (screenshot_file))
        raise Exception("Unexpected BrowserStackLocal test result")
    else:
        assert(result_string.__contains__(regex.lower()))

def existence_lambda(s):
    result = s.find_element_by_accessibility_id("ResultBrowserStackLocal").get_attribute("value")
    return result and len(result) > 0
```

3. You can now run your Behave test using BrowserStack Local with the following command:
```
# Run using paver
paver run local

```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Behave tests at the same time across various device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Behave on BrowserStack, modify the parallel.conf.js config file as shown below:

<Android / IOS Selector>
###### Android (Toggle)

```
{
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "behave-browserstack",
    "name": "parallel_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "Samsung Galaxy S8"
  },
  {
    "device": "Samsung Galaxy S7"
  },
  {
    "device": "Google Nexus 5"
  },
  {
    "device": "Google Pixel"
  }]
}
```

###### iOS (Toggle)

```
{
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "behave-browserstack",
    "name": "parallel_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "iPhone 7"
  }, {
    "device": "iPhone 7 Plus"
  }]
}
```

You can now run your tests in parallel on BrowserStack using the following command:
```
# Run using paver
paver run parallel
```
>Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Behave test cases.

```
import requests
requests.put('https://USERNAME:ACCESS_KEY@api-cloud.browserstack.com/app-automate/sessions/<session-id>.json', data={"status": "completed", "reason": ""})

```
The two potential values for status can either be passed or failed. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/app-automate/appium/api-reference).

### Step 4: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Appium command run through your Behave script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

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
In addition to these logs BrowserStack also provides Raw logs, Network logs and Appium logs. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/app-automate/appium/python-behave/debug-failed-tests)

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
