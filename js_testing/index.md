# Javascript Testing

#### Your guide to running Javascript unit tests on BrowserStack


***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Javascript Unit tests on BrowserStack is simple. This guide will help you:

1. Run your first Javascript unit test
2. Integrate your Javascript unit test framework with BrowserStack
3. Test on internal networks
4. Configure capabilities
5. Speed up your Javascript unit test suite with parallel testing


### Step 1: Run your first Javascript unit test
```
Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [karma-browserstack-example](https://github.com/browserstack/karma-browserstack-example)
```

We can use different Javascript unit testing frameworks such as QUnit, Jasmine, Mocha, and others to write our first unit test. In this sample we are using QUnit.


#### Sample Test Case
We will write a sample test case using [Qunit](https://qunitjs.com/) framework. Follow the below bash commands to set up your test environment.

```
$ mkdir browserstack_js_unit_test
$ cd browserstack_js_unit_test
$ npm init
$ npm install -g qunit
```
Once we have set up the environment, create a file ```odd.js``` in a ```src``` directory. Use the below code:
```
module.exports = function isOdd(val) {
    return val % 2 === 1;
}
```

Now we have our source code, let's add unit test cases for it. Create a file ```test.js``` in a ```tests``` directory.

The unit test checks if a number is odd or not. Use the below code:

```
const isOdd = require('../src/odd.js')
QUnit.test("is 1 odd", function( assert ) {
  assert.ok(isOdd(1), 'One is an odd number');
});
 
QUnit.test("is 2 odd", function( assert ) {
  assert.notOk(isOdd(2), 'two is an even number');
});
```

Run the below command from the root directory:
```
qunit tests
```

You can see the result in your terminal.

Now that we have written the test case, we are ready to run this Javascript unit test on BrowserStack.

#### Run Sample Test Case on BrowserStack
We will use ```karma``` to run the above sample test cases on BrowserStack. Install the below dependencies:
```
$ npm install karma
$ npm install karma-qunit
$ npm install karma-browserstack-launcher
```

Create a file with name ```karma.conf.js``` in the root directory of the project, and use the below code
```
module.exports = function(config) {
  config.set({
 
    // base path that will be used to resolve all patterns (eg. files, exclude)
    basePath: '',
 
 
    // frameworks to use
    frameworks: ['qunit'],
 
    //plugins
    plugins: ['karma-qunit', 'karma-browserstack-launcher'],
 
    // list of files / patterns to load in the browser
    files: [
      'src/*.js',
      'tests/*.js'
    ],
 
    // test results reporter to use
    reporters: ['progress', 'BrowserStack'],
 
 
    // web server port
    port: 9876,
 
 
    // enable / disable colors in the output (reporters and logs)
    colors: true,
 
 
    // level of logging
    logLevel: config.LOG_INFO,
     
    browserStack: {
      username: process.env.BROWSERSTACK_USERNAME,
      accessKey: process.env.BROWSERSTACK_KEY
    },
 
    // define browsers
    customLaunchers: {
      bs_chrome_windows: {
        base: 'BrowserStack',
        browser: 'chrome',
        browser_version: '72.0',
        os: 'Windows',
        os_version: '10'
      }
    },
 
    browsers: ['bs_chrome_windows'],
  })
}
```

Since we are including the source and test files in ```karma.conf.js``` files, change the ```odd.js``` code to not use export:
```
function isOdd(val) {
    return val % 2 === 1;
}
```

Similarly, change the code ```test.js``` file:

```
QUnit.test("is 1 odd", function( assert ) {
  assert.ok(isOdd(1), 'One is an odd number');
});

QUnit.test("is 2 odd", function( assert ) {
  assert.notOk(isOdd(2), 'two is an even number');
});
```

Trigger the test case by using ```./node_modules/karma/bin/karma run``` command. It will run the test on BrowserStack terminal. 

You can check the status of your test on [Automate Dashboard](https://automate.dashboard.com).


### Step 2: Integrate your Javascript unit test framework with BrowserStack
In the above sample test case, we used QUnit unit testing framework. You may already be using a testing framework such as Jasmine, QUnit, Mocha, and others.

[Karma](http://karma-runner.github.io/latest/index.html) is a Javascript test runner, it launches an HTTP server and runs your javascript unit tests on a browser. Please refer [Karma](https://karma-runner.github.io/) website on how to install and setup Karma. You will need to [install a plugin](https://github.com/karma-runner/karma#but-i-still-want-to-use-_insert-testing-library_) specific to your testing framework.


[Karma BrowserStack launcher](https://github.com/karma-runner/karma-browserstack-launcher) integrates with Karma and runs your Javascript unit test cases on BrowserStack grid.


Follow the steps below to integrate with karma-browserstack-launcher

1. Install karma-browserstack-launcher
```
npm install karma-browserstack-launcher --save-dev
```

2. Update your karma.conf.js file with BrowserStack username and access key.
```
// karma.conf.js
module.exports = function(config) {
  config.set({
    // global config of your BrowserStack account
    browserStack: {
      username: 'USERNAME',
      accessKey: 'ACCESS_KEY'
    },
```

3. Configure the list of desktop browser or mobile devices on which you want to run your test. Refer [here](https://www.browserstack.com/list-of-browsers-and-platforms/js_testing) for the complete list of desktop browsers and devices for javascript unit testing.

Refer the below code on how to configure desktop browser and devices.
```
// karma.conf.js
module.exports = function(config) {
  config.set({
    // global config of your BrowserStack account
    browserStack: {
      username: 'USERNAME',
      accessKey: 'ACCESS_KEY'
    },
 
    // define browsers
    customLaunchers: {
      bs_chrome_windows: {
        base: 'BrowserStack',
        browser: 'chrome',
        browser_version: '72.0',
        os: 'Windows',
        os_version: '10'
      },
 
      bs_iphoneX: {
        base: 'BrowserStack',
        device: 'iPhone X',
        os: 'ios',
        real_mobile: true,
        os_version: '11.0'
      }
    },
 
    browsers: ['bs_chrome_windows', 'bs_iphoneX'],
  })
}
```

4. Configure the karma.conf.js file so that test status is automatically marked on BrowserStack. You need to add 'BrowserStack' to the reporters' section.
```
// karma.conf.js
module.exports = function(config) {
  config.set({
    // The rest of your karma config is here
    reporters: ['dots', 'BrowserStack']
  })
}
```

That's it, now you can run your javascript unit tests on BrowserStack. Login to [Automate dashboard](https://automate.browserstack.com) to look at the test status.


### Step 3: Test on internal networks
BrowserStack enables you to run your Javascript unit tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".


Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

There is no need to additionally download browserstack-local. karma-browserstack-launcher has a dependency on browserstack-local and downloads it. You can set local modifiers in the browserStack hash:

```
browserStack: {
    username: 'USERNAME',
    accessKey: 'ACCESS_KEY',
    verbise: 2,
    log-file: '<path of file where you want to log local binary logs>'
},
```
For the complete list of modifiers refer [Local Testing modifiers](https://www.browserstack.com/local-testing#modifiers).


### Step 4: Configuring capabilities
To run your Javascript unit tests on our browsers and devices grid, you have to configure a couple of capabilities so that your tests execute on a remote browser.


#### Run tests on desktop browsers and real mobile devices
 Using the drop-down menus, select a combination of operating system, browser, and screen resolution. You can additionally run your tests on real Android and iOS devices in our datacenters.


Look for the ![](https://www.browserstack.com/images/static/docs/real-device-icon.svg) icon to select a real device.

```os-browser-dropdown goes here```
```
// define browsers
customLaunchers: {
  combination: {
    base: 'BrowserStack',
    browser: 'firefox',
    browser_version: '21.0',
    os: 'OS X',
    os_version: 'Mountain Lion'
  }
},
 
browsers: ['combination']

```

```
Note: If browser_version capability is not set, the test will run on the latest version of the browser set by browser capability.
```

#### Builds and projects
Keep track of all your automated tests using the **build** and **project** capabilities. Group your tests into builds, and builds further into projects.
```
config.set({
    // global config of your BrowserStack account
    browserStack: {
      username: 'USERNAME',
      accessKey: 'ACCESS_KEY',
      'build': 'version1',
      'project': 'newintropage'
    },
```

### Step 5: Speed up your Javascript unit test suite with parallel testing
The BrowserStack grid gives you the infrastructure you need to scale. Features like Parallel Testing and Queuing enable you to scale faster.


#### Parallel Testing
On BrowserStack, you can run multiple Javascript unit tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel on BrowserStack, modify the config file as below:
```
// karma.conf.js
module.exports = function(config) {
  config.set({
    // global config of your BrowserStack account
    browserStack: {
      username: 'USERNAME',
      accessKey: 'ACCESS_KEY'
    },
 
    // define browsers
    customLaunchers: {
      bs_firefox_mac: {
        base: 'BrowserStack',
        browser: 'Chrome',
        browser_version: '72.0',
        os: 'Windows',
        os_version: '10'
      },
      bs_iphone5: {
        base: 'BrowserStack',
        device: 'iPhone 5',
        os: 'ios',
        os_version: '6.0'
      }
    },

    browsers: ['bs_firefox_mac', 'bs_iphone5']
  })
}
```

#### Queuing
With queuing, you can launch an additional number of parallel tests with different browser configurations that will be queued in a sequence. For instance, if you want to run 5 additional tests, apart from your subscribed limit of 2 parallel tests, BrowserStack will queue the additional 5 tests until one of the 2 initial tests finish, and a slot is available for execution. With queuing, you don't need to worry about managing your test pipeline - we automatically take care of scheduling and execution for you.

With this feature, accounts up to 5 parallel tests can queue 5 tests. Beyond 5 parallel tests, an equivalent number of tests will be queued.
```
Note:The wait limit for the execution of a pending queued job is 15 minutes and will be canceled if exceeded.
```





### Next steps
Congrats! You are now able to run your Javascript unit tests on BrowserStack. You may want to check out these resources:
***
#### [Explore BrowserStack API for browser-as-service](https://www.browserstack.com/docs/javascript/browserstack-api)
***
#### [Integrate with CICD Tools]()
***
### Questions?
We're always happy to help with any questions you might have! Read [FAQ](https://www.browserstack.com/support?tag=automate), [Contact support](https://www.browserstack.com/contact), or [connect with our sales team](https://www.browserstack.com/contact#sales).

```
Was this page helpful? Yes No
```
