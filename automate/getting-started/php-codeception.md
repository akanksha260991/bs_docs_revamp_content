# Selenium with Codeception

#### Your guide to running Selenium Webdriver tests with Codeception on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Codeception on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [codeception-browserstack](https://github.com/browserstack/codeception-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with Codeception, ensure you have the Codeception libraries installed:

```
# Install using composer
php composer.phar require codeception/codeception
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in Codeception with PHP
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample Codeception test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
<?php
  // @group single
  $I = new AcceptanceTester($scenario);
  $I->wantTo('Test Google\'s Search Functionality');
  $I->amOnPage('/ncr');
  $I->fillField('q', 'BrowserStack');
  $I->click('btnG');
  $I->seeInTitle('BrowserStack - Google Search');
?>
```

Once we have defined the test case, we are ready to integrate this Codeception test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Integration of Codeception with BrowserStack involves updating the config file with BrowserStack Hub URL and credentials.

```
class_name: AcceptanceTester

modules:
  enabled:
    - WebDriver
  config:
    WebDriver:
      host: 'hub-cloud.browserstack.com'
      port: 80
      browser: chrome
      url: 'http://www.google.com'
      capabilities:
        'browserstack.user': 'USERNAME'
        'browserstack.key' : 'ACCESS_KEY'
```

We are now ready to run the test on BrowserStack, using the following command:

```
vendor/bin/codecept run
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Codeception automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your Codeception tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:
```
# Install using composer
php composer.phar require browserstack/browserstack-local
```

2. Next, you need to update your Codeception config file, and set the browserstack.local capability to true:

```
class_name: AcceptanceTester

modules:
  enabled:
    - \BrowserStackWebDriver
  config:
    \BrowserStackWebDriver:
      host: 'hub-cloud.browserstack.com'
      port: 80
      browser: chrome
      url: 'http://bs-local.com:45691'
      capabilities:
        'browserstack.user': 'USERNAME'
        'browserstack.key' : 'ACCESS_KEY'
        'browserstack.local': true
```
Here is a sample test case written for running local with Codeception.


```
<?php
  // @group local
  $I = new AcceptanceTester($scenario);
  $I->wantTo('Test BrowserStack Local Testing');
  $I->amOnPage('/check');
  $I->see('Up and running');
?>
```

You need the following custom script to launch the test.

```
<?php

require 'vendor/autoload.php';

// here you can define custom actions
// all public methods declared in helper class will be available in $I

class BrowserStackWebDriver extends \Codeception\Module\WebDriver
{
    private $bs_local;

    public function _initialize(){
        getenv('BROWSERSTACK_USERNAME') ? ($this->config["capabilities"]["browserstack.user"] = getenv('BROWSERSTACK_USERNAME')) : 0;
        getenv('BROWSERSTACK_ACCESS_KEY') ? ($this->config["capabilities"]["browserstack.key"] = getenv('BROWSERSTACK_ACCESS_KEY')) : 0;

        if(array_key_exists("browserstack.local", $this->config["capabilities"]) && $this->config["capabilities"]["browserstack.local"])
        {
            $bs_local_args = array("key" => $this->config["capabilities"]["browserstack.key"]);
            $this->bs_local = new BrowserStack\Local();
            $this->bs_local->start($bs_local_args);
        }

        parent::_initialize();
    }

    // HOOK: after suite
    public function _afterSuite() {
        parent::_afterSuite();
        if($this->bs_local) $this->bs_local->stop();
    }
}
```
3. You can now run your Codeception test using BrowserStack Local with the following command:

```
vendor/bin/codecept run
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Codeception tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Codeception on BrowserStack, modify the config file as shown below:
```
class_name: AcceptanceTester

modules:
  enabled:
    - WebDriver
  config:
    WebDriver:
      host: 'hub-cloud.browserstack.com'
      port: 80
      browser: chrome
      url: 'http://www.google.com'
      capabilities:
        'browserstack.user': 'USERNAME'
        'browserstack.key' : 'ACCESS_KEY'

env:
  parallel_0:
    modules:
      config:
        WebDriver:
          browser: 'chrome'

  parallel_1:
    modules:
      config:
        WebDriver:
          browser: 'firefox'

  parallel_2:
    modules:
      config:
        WebDriver:
          browser: 'safari'

  parallel_3:
    modules:
      config:
        WebDriver:
          browser: 'internet explorer'
```
Capabilities for each environment can be customised as explained earlier.

You need install the following dependencies.

```
# Install using composer
php composer.phar require codegyre/robo
php composer.phar require codeception/robo-paracept
```
You need the following custom script to launch the tests in parallel.
```
<?php

require_once 'vendor/autoload.php';

/**
 * This is project's console commands configuration for Robo task runner.
 *
 * @see http://robo.li/
 */
class Robofile extends \Robo\Tasks
{
    use \Codeception\Task\MergeReports;

    private $numParallel = 4;

    public function parallelRun()
    {
        $parallel = $this->taskParallelExec();
        for ($i = 0; $i < $this->numParallel; $i++) {            
            $parallel->process(
                $this->taskCodecept() // use built-in Codecept task
                ->suite('acceptance') // run acceptance tests
                ->env("parallel_$i")          // in its own environment
                ->group("single")
                ->xml("tests/_log/result_$i.xml")
              );
        }
        return $parallel->run();
    }

    function parallelMergeResults()
    {
        $merge = $this->taskMergeXmlReports();
        for ($i=0; $i<$this->numParallel; $i++) {
            $merge->from("tests/_output/tests/_log/result_$i.xml");
        }
        $merge->into("tests/_output/tests/_log/result.xml")
            ->run();
    }

    function parallelAll()
    {
        $result = $this->parallelRun();
        $this->parallelMergeResults();
        return $result;
    }
}
?>
```
Run your tests in parallel on BrowserStack using following command:

```
# Run using robo
vendor/bin/robo parallel:all
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Codeception test cases.

```
file_get_contents('https://USERNAME:ACCESS_KEY@api.browserstack.com/automate/sessions/<session-id>.json', false, stream_context_create(array('http'=>array('method'=>'PUT','header'=>'Content-type: application/json', 'content'=>'{"status":"completed","reason":""}'))));

```
The two potential values for status can either be completed or error. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/selenium/api-reference).


### Step 5: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Codeception tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
capabilities:
  'browserstack.user': 'USERNAME',
  'browserstack.key': 'ACCESS_KEY',
  'browserstack.debug': true
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
capabilities:
  'browserstack.user': 'USERNAME',
  'browserstack.key': 'ACCESS_KEY',
  'browserstack.video': false
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/php-codeception/debug-failed-tests)

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
