# Selenium with Behat

#### Your guide to running Selenium Webdriver tests with Behat on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Behat on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [behat-browserstack](https://github.com/browserstack/behat-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with Behat, ensure you have the Behat libraries installed:

```
# Install using composer
php composer.phar install behat/behat
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in Behat with PHP
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample Behat test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
// Google Feature
Feature: Google Search Functionality

Scenario: Can find search results
    Given I am on "https://www.google.com/ncr"
    When I search for "BrowserStack"
    Then I get title as "BrowserStack - Google Search"
```

```
// Feature Context
<?php

class FeatureContext extends BrowserStackContext {
  /** @Given /^I am on "([^"]*)"$/ */
  public function iAmOnSite($url) {
    self::$driver->get($url);
  }

  /** @When /^I search for "([^"]*)"$/ */
  public function iSearchFor($searchText) {
    $element = self::$driver->findElement(WebDriverBy::name("q"));
    $element->sendKeys($searchText);
    $element->submit();
    sleep(5);
  }

  /** @Then /^I get title as "([^"]*)"$/ */
  public function iShouldGet($string) {
    $title = self::$driver->getTitle();
    if ((string)  $string !== $title) {
      throw new Exception("Expected title: '". $string. "'' Actual is: '". $title. "'");
    }
  }
}
```

Once we have defined the test case, we are ready to integrate this Behat test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

We can now integrate our Behat test case into BrowserStack. Integration of Behat with BrowserStack is made possible by use of following module:

```
class BrowserStackContext extends Behat\Behat\Context\BehatContext
{
    protected $CONFIG;
    protected static $driver;
    private static $bs_local;

    public function __construct($parameters){
        $GLOBALS['CONFIG'] = $parameters["browserstack"];

        $GLOBALS['BROWSERSTACK_USERNAME'] = getenv('BROWSERSTACK_USERNAME');
        if(!$GLOBALS['BROWSERSTACK_USERNAME']) $GLOBALS['BROWSERSTACK_USERNAME'] = $GLOBALS['CONFIG']['user'];

        $GLOBALS['BROWSERSTACK_ACCESS_KEY'] = getenv('BROWSERSTACK_ACCESS_KEY');
        if(!$GLOBALS['BROWSERSTACK_ACCESS_KEY']) $GLOBALS['BROWSERSTACK_ACCESS_KEY'] = $GLOBALS['CONFIG']['key'];
    }

    /** @BeforeFeature */
    public static function setup()
    {
        $CONFIG = $GLOBALS['CONFIG'];
        $task_id = getenv('TASK_ID') ? getenv('TASK_ID') : 0;

        $url = "https://" . $GLOBALS['BROWSERSTACK_USERNAME'] . ":" . $GLOBALS['BROWSERSTACK_ACCESS_KEY'] . "@" . $CONFIG['server'] ."/wd/hub";
        $caps = $CONFIG['environments'][$task_id];

        foreach ($CONFIG["capabilities"] as $key => $value) {
            if(!array_key_exists($key, $caps))
                $caps[$key] = $value;
        }

        if(array_key_exists("browserstack.local", $caps) && $caps["browserstack.local"])
        {
            $bs_local_args = array("key" => $GLOBALS['BROWSERSTACK_ACCESS_KEY']);
            self::$bs_local = new BrowserStack\Local();
            self::$bs_local->start($bs_local_args);
        }

        self::$driver = RemoteWebDriver::create($url, $caps);
    }

    /** @AfterFeature */
    public static function tearDown()
    {
        self::$driver->quit();
        if(self::$bs_local) self::$bs_local->stop();
    }
}
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials.

```
default:
    paths:
        features: '../features/single'
        bootstrap:  '../features/bootstrap'
    context:
        parameters:
            browserstack:
                server: "hub-cloud.browserstack.com"
                user: "USERNAME"
                key: "ACCESS_KEY"

                capabilities:
                    "browserstack.debug": true

                environments:
                    -
                        browser: "chrome"
```

We are now ready to run the test on BrowserStack, using the following command:

```
# Run using composer
composer single
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Behat automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your Behat tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:
```
# Install using composer
php composer.phar install browserstack/browserstack-local
```

2. Next, you need to update your Behat config file, and set the browserstack.local capability to true:

```
default:
    paths:
        features: '../features/local'
        bootstrap:  '../features/bootstrap'
    context:
        parameters:
            browserstack:
                server: "hub-cloud.browserstack.com"
                user: "USERNAME"
                key: "ACCESS_KEY"

                capabilities:
                    "browserstack.local": true

                environments:
                    -
                        browser: "chrome"
```
Here is a sample test case written for running local with Behat.


```
// Local Feature
Feature: BrowserStack Local Testing

Scenario: Can check tunnel working
    Given I am on "http://bs-local.com:45691/check"
    Then I should see "Up and running"
```

```
// Feature Context
class FeatureContext extends BrowserStackContext {
  /** @Given /^I am on "([^"]*)"$/ */
  public function iAmOnSite($url) {
    self::$driver->get($url);
  }

  /** @Then /^I should see "([^"]*)"$/ */
  public function iShouldSee($string) {
    $source = self::$driver->getPageSource();
    if (strpos($source, $string) === false) {
      throw new Exception("Expected to see: '". $string. "'' Actual is: '". $source. "'");
    }
  }
}
```
3. You can now run your Behat test using BrowserStack Local with the following command:

```
# Run using composer
composer local
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Behat tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Behat on BrowserStack, modify the config file as shown below:
```
default:
    paths:
        features: '../features/single'
        bootstrap:  '../features/bootstrap'
    context:
        parameters:
            browserstack:
                server: "hub-cloud.browserstack.com"
                user: "USERNAME"
                key: "ACCESS_KEY"

                capabilities:
                    "browserstack.debug": true

                environments:
                    -
                        browser: "chrome"
                    -
                        browser: "firefox"
                    -
                        browser: "safari"
                    -
                        browser: "internet explorer"
```
Capabilities for each environment can be customised as explained earlier.

You need the following custom script to launch the tests in parallel.
```
$config_file = getenv('CONFIG_FILE');
if(!$config_file) $config_file = 'config/single.conf.yml';
$CONFIG = Yaml::parse(file_get_contents($config_file))["default"]["context"]["parameters"]["browserstack"];

$procs = array();

foreach ($CONFIG['environments'] as $key => $value) {
    $cmd = "TASK_ID=$key ./bin/behat --config=". getenv("CONFIG_FILE")." 2>&1\n";
    print_r($cmd);

    $procs[$key] = popen($cmd, "r");
}

foreach ($procs as $key => $value) {
    while (!feof($value)) {
        print fgets($value, 4096);
    }
    pclose($value);
}
```
Run your tests in parallel on BrowserStack using following command:

```
# Run using composer
composer parallel
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Behat test cases.

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
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Behat tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
capabilities:
  "browserstack.debug": true
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
capabilities:
  "browserstack.video": false
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/php-behat/debug-failed-tests)

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
