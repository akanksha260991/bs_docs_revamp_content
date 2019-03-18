# Selenium with Behave

#### Your guide to running Selenium Webdriver tests with Behave on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Behave on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [behave-browserstack](https://github.com/browserstack/behave-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with Behave, ensure you have the Behave libraries installed:

```
# Install using pip
pip install behave
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in Behave with Python
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample Behave test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
# Google Feature
Feature: Google Search Functionality
    Scenario: can find search results
        When visit url "http://www.google.com/ncr"
        When field with name "q" is given "BrowserStack"
        Then title becomes "BrowserStack - Google Search"
```
```
# Google Steps
@when('visit url "{url}"')
def step(context, url):
    context.browser.get(url)

@when('field with name "{selector}" is given "{value}"')
def step(context, selector, value):
    elem = context.browser.find_element_by_name(selector)
    elem.send_keys(value)
    elem.submit()
    time.sleep(5)

@then('title becomes "{title}"')
def step(context, title):
    assert context.browser.title == title
```

Once we have defined the test case, we are ready to integrate this Behave test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

We can now integrate our Behave test case into BrowserStack. Integration of Behave with BrowserStack is made possible by use of following module:

```
from selenium import webdriver
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
        command_executor="http://%s:%s@%s/wd/hub" % (BROWSERSTACK_USERNAME, BROWSERSTACK_ACCESS_KEY, CONFIG['server'])
    )

def after_feature(context, feature):
    context.browser.quit()
    stop_local()
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials.

```
{
  "server": "hub.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "browserstack.debug": true
  },

  "environments": [{
    "browser": "chrome"
  }]
}
```

We are now ready to run the test on BrowserStack, using the following command:

```
# Run using paver
paver run single
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Behave automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your Behave tests for Local Testing takes just three steps:

1. Install local bindings:
```
# Install using pip
pip install browserstack-local
```

2. Next, you need to update your Behave config file, and set the browserstack.local capability to true:

```
{
  "server": "hub.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "browserstack.local": true
  },

  "environments": [{
    "browser": "chrome"
  }]
}
```
Here is a sample test case written for running local with Behave.


```
# Local Feature
Feature: BrowserStack Local Testing
    Scenario: can check tunnel working
        When visit url "http://bs-local.com:45691/check"
        Then page contains "Up and running"
```
```
# Local Steps
@when('visit url "{url}"')
def step(context, url):
    context.browser.get(url)

@then(u'page contains "{body}"')
def step(context, body):
    assert body in context.browser.page_source
```

3. You can now run your Behave test using BrowserStack Local with the following command:

```
# Run using paver
paver run local
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Behave tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Behave on BrowserStack, modify the config file as shown below:
```
{
  "server": "hub.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "browserstack.debug": true
  },

  "environments": [{
    "browser": "chrome"
  },{
    "browser": "firefox"
  },{
    "browser": "safari"
  },{
    "browser": "internet explorer"
  }]
}
```
Capabilities for each environment can be customised as explained earlier.

Run your tests in parallel on BrowserStack using following command:

```
# Run using paver
paver run parallel
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Behave test cases.

```
import requests
requests.put('https://USERNAME:ACCESS_KEY@api.browserstack.com/automate/sessions/<session-id>.json', data={"status": "completed", "reason": ""})

```
The two potential values for status can either be completed or error. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/selenium/api-reference).


### Step 5: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Behave tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
"capabilities": {
  "browserstack.debug": true
}
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
"capabilities": {
  "browserstack.video": false
}
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/python-behave/debug-failed-tests)

### Next steps
Congrats! You are now able to run your tests on BrowserStack cloud. You may want to check out these resources:
***
#### [Testing on internal networks](https://www.browserstack.com/docs/selenium/<language>-<framework>/getting-started-with-local-testing)
***
#### [Run tests in parallel](https://www.browserstack.com/docs/selenium/<language>-<framework>/getting-started-with-parallel-testing)
***
#### [Debug your app](https://www.browserstack.com/docs/selenium/<language>-<framework>/view-test-results)
***
#### [Integrate with CICD Tools](https://www.browserstack.com/docs/selenium/<language>-<framework>/jenkins)
***
### Questions?
We're always happy to help with any questions you might have! Read [FAQ](https://www.browserstack.com/support?tag=automate), [Contact support](https://www.browserstack.com/contact), or [connect with our sales team](https://www.browserstack.com/contact#sales).

```
Was this page helpful? Yes No
```
