# Appium with Cucumber

#### Your guide to running mobile app tests using Appium with Cucumber on BrowserStack real device cloud.
***
BrowserStack gives you instant access to 2000+ real devices. Running your Appium test automation with Cucumber for native and hybrid mobile apps on BrowserStack is simple. This guide will help you:

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
Ensure you have Cucumber libraries installed.
```
# Install using gem
gem install cucumber
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

Different ways you can upload your app can be found [here](/docs/app-automate/appium/python-lettuce/upload-app).

#### 3. Configure and run test
Copy sample code provided in Cucumber for Android and iOS. Update the desired capability "app" with the App URL returned from the above API call.

<Android/iOS selector>
##### Android (Toggle)

If you are using our Sample App, the sample test below will install the Sample App (Wikipedia App) on the device, search for 'browserstack' and asserts for the list of results. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for Cucumber on Github: [cucumber-appium-app-browserstack](https://github.com/browserstack/cucumber-appium-app-browserstack)

```
# Wikipedia Feature
Feature: Search in Wikipedia App

Background:
  Given I try to search using Wikipedia App

Scenario: Search for a term
  When I type in "BrowserStack"
  Then I should see results
```
```
When /^I try to search using Wikipedia App/ do
  $wait.until { $driver.find_element(:accessibility_id, "Search Wikipedia").displayed? }
  $driver.find_element(:accessibility_id, "Search Wikipedia").click
end

When("I type in {string}") do |search_keyword|
  $wait.until { $driver.find_element(:id, "org.wikipedia.alpha:id/search_src_text").displayed? }
  search_box = $driver.find_element(:id, "org.wikipedia.alpha:id/search_src_text")
  search_box.send_key(search_keyword)
  sleep 5
end

Then /^I should see results$/ do
  $driver.find_elements(:class, "android.widget.TextView").size.should > 0
end
```
> Note:
> 1. UIAutomator2 is the default automation engine for Android.
> 2. Pass automationName as 'Appium' if you are using Appium automation engine
> 3. If your app uses webview, set the webview debugging flag to true when creating the webview object as described in the [Android remote debugging docs](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews): WebView.setWebContentsDebuggingEnabled(true);

##### iOS (Toggle)
If you are using our iOS Sample App, the sample test below will install the Sample App (WordPress App) on the device, navigate to the Login screen, enters the login email and check whether the email is registered on WordPress. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for Lettuce on Github: [lettuce-appium-app-browserstack](https://github.com/browserstack/lettuce-appium-app-browserstack)

```
# WordPress Feature
Feature: Verify if displayed text matches entered text

Background:
  Given I try to find Text Button in Sample App

Scenario: Type a term
  When I type in "hello@browserstack.com" in the Text Input field
  Then I should get the entered text in the Text Output field
```
```
When /^I try to find Text Button in Sample App/ do
  $wait.until { $driver.find_element(:accessibility_id, "Text Button").displayed? }
  $driver.find_element(:accessibility_id, "Text Button").click
end

When("I type in {string} in the Text Input field") do |text|
  puts "Received #{text}"
  $wait.until { $driver.find_element(:accessibility_id, "Text Input").displayed? }
  text_input = $driver.find_element(:accessibility_id, "Text Input")
  text_input.send_keys(text+"\n")
  sleep 5
end

Then /^I should get the entered text in the Text Output field$/ do
  sleep 5
  text_output = $driver.find_element(:accessibility_id, "Text Output")
  text_output.text.should eq("hello@browserstack.com")
end
```

>Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.

To actually run the test case, we need to integrate with BrowserStack as follows.

##### Integration with BrowserStack
Integration of Cucumber with BrowserStack is made possible by use of following module:

```
require 'yaml'
require 'rspec'
require 'selenium-cucumber'
require 'browserstack/local'
require 'appium_lib'

TASK_ID = (ENV['TASK_ID'] || 0).to_i
CONFIG_NAME = ENV['CONFIG_NAME'] || 'single'

CONFIG = YAML.load(File.read(File.join(File.dirname(__FILE__), "../../config/#{CONFIG_NAME}.config.yml")))
CONFIG['user'] = ENV['BROWSERSTACK_USERNAME'] || CONFIG['user']
CONFIG['key'] = ENV['BROWSERSTACK_ACCESS_KEY'] || CONFIG['key']

caps = CONFIG['common_caps'].merge(CONFIG['browser_caps'][TASK_ID])
$bs_local = nil

if ENV['BROWSERSTACK_APP_ID']
  caps['app'] = ENV['BROWSERSTACK_APP_ID']
end

if caps['browserstack.local'] && caps['browserstack.local'].to_s == 'true'
  $bs_local = BrowserStack::Local.new
  bs_local_args = { "key" => "#{CONFIG['key']}" }
  $bs_local.start(bs_local_args)
end

desired_caps = {
  caps: caps,
  appium_lib: {
    server_url: "http://#{CONFIG['user']}:#{CONFIG['key']}@#{CONFIG['server']}/wd/hub"
  }
}

begin
  $appium_driver = Appium::Driver.new(desired_caps, true)
  $driver = $appium_driver.start_driver
rescue Exception => e
  puts e.message
  Process.exit(0)
end

$wait = Selenium::WebDriver::Wait.new(:timeout => 30)
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials. If the BROWSERSTACK_USERNAME and BROWSERSTACK_ACCESS_KEY are set in Environment variables, then that will take a preference. Also, update the App URL after uploading App to the BrowserStack cloud.

<Android / IOS Selector>
###### Android (Toggle)
```
server: "hub-cloud.browserstack.com"
user: "USERNAME"
key: "ACCESS_KEY"

common_caps:
  "build": "cucumber-browserstack"
  "browserstack.debug": true

browser_caps:
  -
    "device": "Google Pixel"
    "app": "bs://<hashed app-id>"
    "name": "single_test"
```
###### iOS (Toggle)
```
server: "hub-cloud.browserstack.com"
user: "USERNAME"
key: "ACCESS_KEY"

common_caps:
  "build": "cucumber-browserstack"
  "browserstack.debug": true

browser_caps:
  -
    "device": "iPhone 7 Plus"
    "app": "bs://<hashed app-id>"
    "name": "single_test"
```

Run your test on BrowserStack using following command:

```
# Run using rake
bundle exec rake single
```

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

>Note:  In order to try a local test, upload our Local Sample App for [Android](https://www.browserstack.com/app-automate/sample-apps/android/LocalSample.apk) and [iOS](https://www.browserstack.com/app-automate/sample-apps/ios/LocalSample.ipa)

Configuring your Lettuce tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:

```
# Install using pip
pip install browserstack-local
```
2. Next, you need to update your Lettuce config file, and set the browserstack.local capability to true:

<Android / IOS Selector>
###### Android (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "lettuce-browserstack",
    "name": "single_test",
    "browserstack.local": true,
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "devices": [{
    "device": "Google Pixel"
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
    "build": "lettuce-browserstack",
    "name": "local_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>",
    "browserstack.local": true
  },

  "devices": [{
    "device": "iPhone 7"
  }]
}
```

Here is a sample test case written for running local with Lettuce.

```
# Local Feature
Feature: BrowserStack Local Testing
    Scenario: can check tunnel working
        Given I open app and click on button
            Then page contains "Up and running"
```
```
@step(u'I open app and click on button')
def click_on_button(step):
    with AssertContextManager(step):
        test_button = WebDriverWait(world.browser, 30).until(
            EC.element_to_be_clickable((MobileBy.ACCESSIBILITY_ID, "TestBrowserStackLocal"))
        )
        test_button.click()


@step(u'Then page contains "([^"]*)"')
def then_page_contains(step, regex):
    WebDriverWait(world.browser, 30).until(existence_lambda)
    result_element = world.browser.find_element_by_accessibility_id("ResultBrowserStackLocal")
    result_string = result_element.text.lower()

    if result_string.__contains__("not working"):
        screenshot_file = "%s/screenshot.png" % os.getcwd()
        driver.save_screenshot(screenshot_file)
        print "Screenshot stored at %s" % screenshot_file
        raise Exception("Unexpected BrowserStackLocal test result")
    else:
        assert(result_string.__contains__(regex.lower()))

def existence_lambda(s):
    result = s.find_element_by_accessibility_id("ResultBrowserStackLocal").get_attribute("value")
    return result and len(result) > 0
```

3. You can now run your Lettuce test using BrowserStack Local with the following command:
```
# Run using paver
paver run local

```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Lettuce tests at the same time across various device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Lettuce on BrowserStack, modify the parallel.conf.js config file as shown below:

<Android / IOS Selector>
###### Android (Toggle)

```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "lettuce-browserstack",
    "name": "single_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "devices": [{
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
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "lettuce-browserstack",
    "name": "parallel_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "devices": [{
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
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Lettuce test cases.

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
Visual Logs automatically capture the screenshots generated at every Appium command run through your Lettuce script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

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
In addition to these logs BrowserStack also provides Raw logs, Network logs and Appium logs. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/app-automate/appium/python-lettuce/debug-failed-tests)

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
