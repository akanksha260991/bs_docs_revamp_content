# Selenium with Cucumber JS

#### Your guide to running Selenium Webdriver tests with Cucumber JS on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Cucumber JS on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [cucumber-js-browserstack](https://github.com/browserstack/cucumber-js-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with Cucumber JS, Install Cucumber JS using npm

```
# Install using npm
npm install cucumber
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in Cucumber JS with NodeJS
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample Cucumber JS test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
// Google Feature
Feature: Google Search Functionality

  Scenario: Can find search results
    When I type query as "BrowserStack"
    Then I submit
    Then I should see title "BrowserStack - Google Search"
```
Once we have defined the feature file, which contains the test case, we now need to create the step definition. The step definition for the Cucumber JS test case shown above is:

```
// Google Steps
module.exports = function() {

  this.When(/^I type query as "([^"]*)"$/, function (searchQuery, next) {
    this.driver.get('https://www.google.com/ncr');
    this.driver.findElement({ name: 'q' })
      .sendKeys(searchQuery + '\n').then(next);
  });

  this.Then(/^I submit$/, function (next) {
    var self = this;
    this.driver.findElement({ name: 'btnG' })
      .click()
      .then(function() {
        self.driver.wait(function () {
          return self.driver.isElementPresent(webdriver.By.id("top_nav"));
        }, 5000);
        next();
      });
  });

  this.Then(/^I should see title "([^"]*)"$/, function (titleMatch, next) {
    this.driver.getTitle()
      .then(function(title) {
        assert.equal(title, titleMatch, next, 'Expected title to be ' + titleMatch);
      });
  });
};
```
Now that we have created the feature file and the step definition, we are ready to integrate this Cucumber JS test case into BrowserStack.


#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Now that we have created a feature file and step definitions, we can integrate our Cucumber JS test case into BrowserStack. We now create a hooks.js and env.js file, as show below:

```
// hooks.js
var config_file = '../../conf/' + (process.env.CONFIG_FILE || 'single') + '.conf.js';
var config = require(config_file).config;

var username = process.env.BROWSERSTACK_USERNAME || config.user;
var accessKey = process.env.BROWSERSTACK_ACCESS_KEY || config.key;

var createBrowserStackSession = function(config, caps){
  return new webdriver.Builder().
    usingServer('http://'+config.server+'/wd/hub').
    withCapabilities(caps).
    build();
}

var myHooks = function () {
  var bs_local = null;

  this.Before(function (scenario, callback) {
    var world = this;
    var task_id = parseInt(process.env.TASK_ID || 0);
    var caps = config.capabilities[task_id];
    caps['browserstack.user'] = username;
    caps['browserstack.key'] = accessKey;

    if(caps["browserstack.local"]){
      // Code to start browserstack local before start of test and stop browserstack local after end of test
      bs_local = new browserstack.Local();
      bs_local.start({'key': accessKey }, function(error) {
        if (error) return console.log(error.red);

        world.driver = createBrowserStackSession(config, caps);
        callback();
      });
    }
    else {
      world.driver = createBrowserStackSession(config, caps);
      callback();
    }
  });

  this.After(function(scenario, callback){
    this.driver.quit().then(function(){
      if(bs_local){
        bs_local.stop(callback);
      }
      else callback();
    });
  });
};

module.exports = myHooks;
```
```
// env.js
var configure = function () {
    this.setDefaultTimeout(60 * 1000);
};

module.exports = configure;
```
We now need to create a config file, which contains the BrowserStack Hub URL and credentials required to connect to the BrowserStack Selenium grid.

```
exports.config = {
  user: 'USERNAME',
  key: 'ACCESS_KEY',
  server: 'hub-cloud.browserstack.com',

  capabilities: [{
    browserName: 'chrome'
  }]
}
```
We are now ready to run the test on BrowserStack, using the following command:

```
CONFIG_FILE=single ./node_modules/cucumber/bin/cucumber.js features/single.feature
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Cucumber JS automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your Cucumber JS tests for Local Testing takes just three steps:

1. Download and run the BrowserStackLocal binary:

```
# Install using npm
npm install browserstack-local
```

2. Next, you need to update your Cucumber JS config file, local.conf.json, and set the browserstack.local capability to true:

```
exports.config = {
  user: 'USERNAME',
  key: 'ACCESS_KEY',
  server: 'hub-cloud.browserstack.com',

  capabilities: [{
    browserName: 'chrome',
    'browserstack.local': true
  }]
}
```
Here is a sample feature and steps file which using Local testing:

```
// Local Feature
Feature: BrowserStack Local Testing

  Scenario: Can check tunnel working
    When I open health check
    Then I should see "Up and running"
```

```
// Local Steps
'use strict';

var assert = require('cucumber-assert');
var webdriver = require('selenium-webdriver');

module.exports = function() {

  this.When(/^I open health check$/, function (next) {
    this.driver.get('http://bs-local.com:45691/check');
    next();
  });

  this.Then(/^I should see "([^"]*)"$/, function (sourceMatch, next) {
    this.driver.getPageSource()
      .then(function(source) {
        assert.equal(source.indexOf(sourceMatch) > -1, true, next, 'Expected source to contain ' + sourceMatch);
      });
  });
};
```
3. You can now run your Cucumber JS test using BrowserStack Local with the following command:

```
CONFIG_FILE=local ./node_modules/cucumber/bin/cucumber.js features/local.feature
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Cucumber JS tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Cucumber JS on BrowserStack, modify the config file as below:
```
exports.config = {
  user: 'USERNAME',
  key: 'ACCESS_KEY',
  server: 'hub-cloud.browserstack.com',

  commonCapabilities: {
    "browserstack.debug": true
  },

  capabilities: [{
    browserName: 'chrome'
  },{
    browserName: 'firefox'
  },{
    browserName: 'safari'
  },{
    browserName: 'internet explorer'
  }]
}

// Code to support common capabilities
exports.config.capabilities.forEach(function(caps){
  for(var i in exports.config.commonCapabilities) caps[i] = caps[i] || exports.config.commonCapabilities[i];
});
```
Capabilities for each environment can be customised as explained earlier.

You need the following custom script to launch the tests in parallel.
```
#!/usr/bin/env node
var child_process = require('child_process');
var config_file = '../conf/' + (process.env.CONFIG_FILE || 'single') + '.conf.js';
var config = require(config_file).config;

process.argv[0] = 'node';
process.argv[1] = './node_modules/.bin/cucumber-js';

for(var i in config.capabilities){
  var env = Object.create( process.env );
  env.TASK_ID = i.toString();
  var p = child_process.spawn('/usr/bin/env', process.argv, { env: env } );  
  p.stdout.pipe(process.stdout);
}
```
You can now run your tests in parallel on BrowserStack using the following command:

```
# Run using custom runner
CONFIG_FILE=parallel ./scripts/cucumber-runner.js features/single.feature
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Cucumber JS test cases.

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
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Cucumber JS tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
capabilities: [{
  'browserstack.debug': true
}]
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
capabilities: [{
  'browserstack.video': false
}]
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/nodejs-cucumber-js/debug-failed-tests)

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
