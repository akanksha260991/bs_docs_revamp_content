# Selenium with WD

#### Documentation for running Selenium Tests with BrowserStack.


***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with WD on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [wd-browserstack](https://github.com/browserstack/wd-browserstack)

### Prerequisites:
For the code to run successfully on your machine, please ensure that the following libraries have been installed:

```
# Install using npm
npm install wd
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in WD with NodeJS
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample WD test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
module.exports = {
  name: 'Google\'s Search Functionality',
  run : function (browser) {
    return browser
      .get("https://www.google.com/ncr")
      .elementByName('q')
      .sendKeys('BrowserStack\n')
      .sleep(5000)
      .title()
        .should.become('BrowserStack - Google Search');
  }
};
```
Now that we have created the test case, we are ready to integrate it into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Integration of WD with BrowserStack is made possible by use of following module:

```
var chai = require("chai"),
    chaiAsPromised = require("chai-as-promised"),
    wd = require('wd'),
    colors = require('colors'),
    child_process = require('child_process'),
    browserstack = require('browserstack-local');

chai.use(chaiAsPromised);
chai.should();
chaiAsPromised.transferPromiseness = wd.transferPromiseness;

wd.addPromiseChainMethod(
  'onQuit', function(done) {
    if(done) done();
    return this;
  }
);

function runOnBrowserStack(caps, test, done){
  console.log("Running Test: " + test.name.green + '\n');
  var browser = wd.promiseChainRemote(config.seleniumHost, config.seleniumPort, username, accessKey);

  // optional extra logging
  browser.on('status', function(info) {
    console.log(info.cyan);
  });
  browser.on('command', function(eventType, command, response) {
    console.log(' > ' + eventType.green, command, (response || '').grey);
  });
  browser.on('http', function(meth, path, data) {
    console.log(' > ' + meth.yellow, path, (data || '').grey);
  });

  test.run(browser.init(caps)).fin(function() { return browser.quit(); }).onQuit(done).done();
}

var config_file = process.argv[2] || 'conf.js'
var config = require(config_file).config;
var test = require(config.test);

var username = process.env.BROWSERSTACK_USERNAME || config.user;
var accessKey = process.env.BROWSERSTACK_ACCESS_KEY || config.key;

for(var i in config.capabilities){
  var caps = config.capabilities[i];
  if(caps["browserstack.local"]){
    // Code to start browserstack local before start of test and stop browserstack local after end of test
    console.log("Connecting local");
    var bs_local = new browserstack.Local();
    bs_local.start({'key': accessKey }, function(error) {
      if (error) return console.log(error.red);
      console.log('Connected. Now testing...');

      runOnBrowserStack(caps, test, function(){ bs_local.stop(); });
    });
  }
  else {
    runOnBrowserStack(caps, test);
  }
}
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials.

```
exports.config = {
  user: 'kalpeshdoshi3',
  key: 'HHYWUpopN7yycfrR4hEW',

  seleniumHost: 'hub-cloud.browserstack.com',
  seleniumPort: 80,

  capabilities: [{
    browserName: 'chrome'
  }]
}
```
Run your test on BrowserStack using following command:

```
# Run using npm
npm run single
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Protractor automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your WD tests for Local Testing takes just three steps:

1. Install the BrowserStack Local Testing bindings:

```
# Install using npm
npm install browserstack-local
```

2. Next, you need to update your WD config file, local.conf.json, and set the browserstack.local capability to true:

```
exports.config = {
  user: 'USERNAME',
  key: 'ACCESS_KEY',

  seleniumHost: 'hub-cloud.browserstack.com',
  seleniumPort: 80,

  capabilities: [{
    browserName: 'chrome',
    'browserstack.local': true
  }]
}
```
You can use the following sample test case, which is in our GitHub repo. This test case navigates to the local server which you can use to verify if your Local test connection has been setup correctly:

```
module.exports = {
  name: 'BrowserStack Local Testing',
  run : function (browser) {
    return browser
      .get("http://bs-local.com:45691/check")
      .source()
      .then(function(data){
        data.should.match(/Up and running/i);
      })
  }
};
```
3. You can now run your WD test using BrowserStack Local with the following command:

```
# Run using npm
npm run local
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple WD tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with WD on BrowserStack, modify the parallel.conf.js config file as shown below:
```
exports.config = {
  user: 'USERNAME',
  key: 'ACCESS_KEY',

  seleniumHost: 'hub-cloud.browserstack.com',
  seleniumPort: 80,

  commonCapabilities: [{
    "browserstack.debug": true
  }],

  "capabilities": [{
    "browser": "chrome"
  },{
    "browser": "firefox"
  },{
    "browser": "safari"
  },{
    "browser": "internet explorer"
  }]
}

// Code to support common capabilities
exports.config.capabilities.forEach(function(caps){
  for(var i in exports.config.commonCapabilities) caps[i] = caps[i] || exports.config.commonCapabilities[i];
});
```
Capabilities for each environment can be customised as explained earlier.

You can now run your tests in parallel on BrowserStack using the following command:
```
# Run using npm
npm run parallel

```

> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your WD test cases.

```
var request = require("request");
request({uri: "https://USERNAME:ACCESS_KEY@api.browserstack.com/automate/sessions/<session-id>.json", method:"PUT", form:{"status":"completed","reason":""}})
```
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/selenium/api-reference).


### Step 5: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your WD tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
"capabilities": [{
  "browserstack.debug": true
}]
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
"capabilities": [{
  "browserstack.video": false
}]
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/nodejs-wd/debug-failed-tests)

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
