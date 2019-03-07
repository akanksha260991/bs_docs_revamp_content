# Selenium with Nightwatch

#### Your guide to running Selenium Webdriver tests with Nightwatch on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Nightwatch on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [nightwatch-browserstack](https://github.com/browserstack/nightwatch-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with Nightwatch, Install Nightwatch using npm

```
# Install using npm
npm install nightwatch
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in Nightwatch with NodeJS
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample Nightwatch test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
module.exports = {
  'Google\'s Search Functionality' : function (browser) {
    browser
      .url('https://www.google.com/ncr')
      .waitForElementVisible('body', 1000)
      .setValue('input[type=text]', 'BrowserStack\n')
      .pause(1000)
      .assert.title('BrowserStack - Google Search')
      .end();
  }
};
```

Now that we have created the test case, we are ready to integrate it into BrowserStack.


#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

To integrate the sample Nightwatch test case into BrowserStack, we have to update the \*.conf.js config files with the BrowserStack Hub URL and credentials required to connect to the BrowserStack Selenium grid. The example below shows the single.conf.js config file, used for single test runs:

```
nightwatch_config = {
  selenium : {
    "start_process" : false,
    "host" : "hub-cloud.browserstack.com",
    "port" : 80
  },

  test_settings: {
    default: {
      desiredCapabilities: {
        'browserstack.user': 'USERNAME',
        'browserstack.key': 'ACCESS_KEY',
        'browser': 'chrome'
      }
    }
  }
};

// Code to copy seleniumhost/port into test settings
for(var i in nightwatch_config.test_settings){
  var config = nightwatch_config.test_settings[i];
  config['selenium_host'] = nightwatch_config.selenium.host;
  config['selenium_port'] = nightwatch_config.selenium.port;
}

module.exports = nightwatch_config;
```
We are now ready to run the test on BrowserStack, using the following command:

```
./node_modules/.bin/nightwatch -c conf/single.conf.js
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Nightwatch automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your Nightwatch tests for Local Testing takes just three steps:

1. Download and run the BrowserStackLocal binary:

```
# Install using npm
npm install browserstack-local
```

2. Next, you need to update your Nightwatch config file, local.conf.json, and set the browserstack.local capability to true:

```
nightwatch_config = {
  selenium : {
    "start_process" : false,
    "host" : "hub-cloud.browserstack.com",
    "port" : 80
  },

  test_settings: {
    default: {
      desiredCapabilities: {
        'browserstack.user': 'USERNAME',
        'browserstack.key': 'ACCESS_KEY',
        'browserstack.local': true,
        'browser': 'chrome'
      }
    }
  }
};

// Code to copy seleniumhost/port into test settings
for(var i in nightwatch_config.test_settings){
  var config = nightwatch_config.test_settings[i];
  config['selenium_host'] = nightwatch_config.selenium.host;
  config['selenium_port'] = nightwatch_config.selenium.port;
}

module.exports = nightwatch_config;
```
You can use the following sample test case, which is in our GitHub repo. This test case navigates to the local server which you can use to verify if your Local test connection has been setup correctly:

```
module.exports = {
  'BrowserStack Local Testing' : function (browser) {
    browser
      .url('http://bs-local.com:45691/check')
      .waitForElementVisible('body', 1000)
      .assert.containsText('body', 'Up and running')
      .end();
  }
};
```
You will also need the following custom script, local.runner.js:

```
#!/usr/bin/env node

var Nightwatch = require('nightwatch');
var browserstack = require('browserstack-local');
var bs_local;

try {
  process.mainModule.filename = "./node_modules/.bin/nightwatch"

  // Code to start browserstack local before start of test
  console.log("Connecting local");
  Nightwatch.bs_local = bs_local = new browserstack.Local();
  bs_local.start({'key': process.env.BROWSERSTACK_ACCESS_KEY }, function(error) {
    if (error) throw error;

    console.log('Connected. Now testing...');
    Nightwatch.cli(function(argv) {
      Nightwatch.CliRunner(argv)
        .setup(null, function(){
          // Code to stop browserstack local after end of parallel test
          bs_local.stop(function(){});
        })
        .runTests(function(){
          // Code to stop browserstack local after end of single test
          bs_local.stop(function(){});
        });
    });
  });
} catch (ex) {
  console.log('There was an error while starting the test runner:\n\n');
  process.stderr.write(ex.stack + '\n');
  process.exit(2);
}
```
3. You can now run your Nightwatch test using BrowserStack Local with the following command:

```
# Run using custom runner
./scripts/local.runner.js -c conf/local.conf.js
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Nightwatch tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Nightwatch on BrowserStack, modify the parallel.conf.js config file as shown below:
```
nightwatch_config = {
  src_folders : [ "tests/single" ],

  selenium : {
    "start_process" : false,
    "host" : "hub-cloud.browserstack.com",
    "port" : 80
  },

  common_capabilities: {
    'browserstack.user': 'USERNAME',
    'browserstack.key': 'ACCESS_KEY'
  },

  test_settings: {
    default: {},
    chrome: {
      desiredCapabilities: {
        browser: "chrome"
      }
    },
    firefox: {
      desiredCapabilities: {
        browser: "firefox"
      }
    },
    safari: {
      desiredCapabilities: {
        browser: "safari"
      }
    },
    ie: {
      desiredCapabilities: {
        browser: "internet explorer"
      }
    }
  }
};

// Code to support common capabilites
for(var i in nightwatch_config.test_settings){
  var config = nightwatch_config.test_settings[i];
  config['selenium_host'] = nightwatch_config.selenium.host;
  config['selenium_port'] = nightwatch_config.selenium.port;
  config['desiredCapabilities'] = config['desiredCapabilities'] || {};
  for(var j in nightwatch_config.common_capabilities){
    config['desiredCapabilities'][j] = config['desiredCapabilities'][j] || nightwatch_config.common_capabilities[j];
  }
}

module.exports = nightwatch_config;
```
Capabilities for each environment can be customised as explained earlier.

You can now run your tests in parallel on BrowserStack using the following command:
```
./node_modules/.bin/nightwatch -c conf/parallel.conf.js  -e chrome,firefox,safari,ie
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Nightwatch test cases.

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
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Nightwatch tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
desiredCapabilities: {
  'browserstack.user': 'USERNAME',
  'browserstack.key': 'ACCESS_KEY',
  'browserstack.debug': true
}
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
desiredCapabilities: {
  'browserstack.user': 'USERNAME',
  'browserstack.key': 'ACCESS_KEY',
  'browserstack.video': false
}
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/nodejs-nightwatch/debug-failed-tests)

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
