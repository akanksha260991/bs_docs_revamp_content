# Selenium with NodeJS

#### Your guide to running Selenium Webdriver tests with NodeJS on BrowserStack.
***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with NodeJS on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app


**Prerequisites:**

Before you can start running your Selenium tests with Node JS, ensure the following libraries are installed:

```
//Use npm package manager to install selenium web driver
npm install -g selenium-webdriver
```



***

### Step 1: Run your first test

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

To get started, let’s run a simple Selenium Webdriver test. The NodeJS script below will open a URL, input a string, submit the form, and return the page title.

***First***, select the OS and Device/Browser combination you'd like to test on using the drop-down menus below. This will automatically update the NodeJS code sample below:

<OS / Device Selector>

Look for the <real device> icon to select a real device.

> Note: Testing on real devices requires the [Automate Mobile plan](https://www.browserstack.com/pricing?tab=browser-plans-tab)

```
var webdriver = require('selenium-webdriver');

// Input capabilities
var capabilities = {
 'browserName' : 'iPhone',
 'device' : 'iPhone 8 Plus',
 'realMobile' : 'true',
 'os_version' : '11',
 'browserstack.user' : 'USERNAME',
 'browserstack.key' : 'ACCESS_KEY'
}

var driver = new webdriver.Builder().
  usingServer('http://hub-cloud.browserstack.com/wd/hub').
  withCapabilities(capabilities).
  build();

driver.get('http://www.google.com').then(function(){
  driver.findElement(webdriver.By.name('q')).sendKeys('BrowserStack\n').then(function(){
    driver.getTitle().then(function(title) {
      console.log(title);
      driver.quit();
    });
  });
});
```
> Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.


***Second***, copy-and-paste the code sample into your code editor, save it as a .js file, and execute the test from your command line.

***Third***, verify the results. The Selenium Webdriver test should have opened a URL, inputted a string, submitted the form, and returned the page title. Your results will be displayed on the command-line interface and on the [Automate dashboard](https://automate.browserstack.com), where you can see Text Logs, Screenshots of every Selenium command, and a Video Recording of your entire test.

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

In two steps, configure your Selenium Webdriver tests for Local Testing:

1. Download and run the BrowserStackLocal binary:

  Download the appropriate binary:

  - OS X (Recommended for your system)
  - Linux 32-bit
  - Linux 64-bit
  - Windows

  The download links are secure. The binaries are digitally signed, identifying the publisher as BrowserStack Ltd. Read more about our [security](https://www.browserstack.com/security#local-testing).

  Navigate to the folder containing the binary, and run it from the command line.

  <OS X & Linux / Windows selector>

  **OS X & Linux**
  ```
  ./BrowserStackLocal --key ekKTGeFduvsvXvXx96xG
  ```

  **Windows**
  ```
  BrowserStackLocal.exe --key ekKTGeFduvsvXvXx96xG
  ```
2. Once the connection is established, enable local testing in your tests by setting the browserstack.local capability to true.
```
var capabilities = {
  'browserstack.local' : 'true'
}
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Selenium Webdriver tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application.

With Parallel Testing, you can run the same test on different browser/device combinations, or run different tests on the same browser/device combination. Parallel Testing will help you reduce the run time of your test suite, resulting in faster build times and faster releases.

To start Parallel testing, you can use any of the popular test frameworks which work with NodeJS and Selenium. We have provided getting started guides on some of the popular frameworks below:
- [Cucumber JS](https://www.browserstack.com/automate/cucumberjs)
- [Intern](https://www.browserstack.com/automate/intern)
- [Nightwatch](https://www.browserstack.com/automate/nightwatch)
- [Protractor](https://www.browserstack.com/automate/protractor)
- [WD](https://www.browserstack.com/automate/wd)
- [WebdriverIO](https://www.browserstack.com/automate/webdriverio)

### Step 4: Mark tests as pass / fail
It is possible to mark tests as either a pass or a fail, using the following snippet:

```
var request = require("request");
request({uri: "https://USERNAME:ACCESS_KEY@api.browserstack.com/automate/sessions/<session-id>.json", method:"PUT", form:{"status":"passed","reason":""}})
}
```
The two potential values for status can either be passed or failed. Optionally, a reason can also be passed.

### Step 4: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/automate/rest-api).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your NodeJS script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
var capabilities = {
  'browserstack.debug' : 'true'
}
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/automate/rest-api).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
var capabilities = {
  'browserstack.video' : 'false'
}
```
**Console Logs**
Console Logs capture the browser's console output at various steps of the test to troubleshoot javascript issues. You can retrieve Console Logs using our [REST API](https://www.browserstack.com/automate/rest-api). You will also be able to download logs from Automate Dashboard.

Console Logs are enabled with log level set to 'errors' by default. To set different log levels, you need to use the capability browserstack.console with values 'disable', 'errors', 'warnings', 'info' or 'verbose', as shown below:
```
var capabilities = {
  'browserstack.console' : 'warnings'
}
```
**Network Logs**
Network Logs capture the browser's performance data such as network traffic, latency, HTTP requests and responses in the HAR format. You can download network logs using the REST API or from the Automate Dashboard. You can visualize HAR files using the HAR Viewer.

Network Logs are disabled by default. To enable Network Logs use the capability **browserstack.networkLogs** with the value **'true'**, as shown below:
```
var capabilities = {
  'browserstack.networkLogs' : 'true'
}
```

### Next steps
Congrats! You are now able to run your tests on BrowserStack cloud. You may want to check out these resources:
***
#### [Testing on internal networks](https://www.browserstack.com/docs/automate/getting-started-with-local-testing?lang=<language>&framework=<framework>)
***
#### [Run tests in parallel](https://www.browserstack.com/docs/automate/getting-started-with-parallel-testing?lang=<language>&framework=<framework>)
***
#### [Debug your app](https://www.browserstack.com/docs/automate/view-test-results?lang=<language>&framework=<framework>)
***
#### [Integrate with CICD Tools](https://www.browserstack.com/docs/automate/jenkins?lang=<language>&framework=<framework>)
***
### Questions?
We're always happy to help with any questions you might have! Read [FAQ](https://www.browserstack.com/support?tag=automate), [Contact support](https://www.browserstack.com/contact), or [connect with our sales team](https://www.browserstack.com/contact#sales).

```
Was this page helpful? Yes No
```
