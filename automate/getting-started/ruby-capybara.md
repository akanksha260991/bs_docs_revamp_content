# Selenium with Capybara

#### Your guide to running Selenium Webdriver tests with Capybara on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Capybara on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [capybara-browserstack](https://github.com/browserstack/capybara-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with Capybara, ensure you have the Capybara libraries installed:

```
# Install using gem
gem install capybara
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in Capybara with Ruby
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample Capybara test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
# Google Feature
Feature: Google Search Functionality

Background:
  Given I am on https://www.google.com/ncr

Scenario: Can find search results
  When I fill in "q" found by "name" with "BrowserStack"
  And I submit
  Then I should see title "BrowserStack - Google Search"
```
```
# Google Steps
Given /^I am on (.*)$/ do |url|
  visit url
end

When /^I fill in "([^\"]*)" found by "([^\"]*)" with "([^\"]*)"$/ do |value, type, keys|
  fill_in(value, :with => keys)
end

When /^I submit$/ do
  find_field('q').native.send_key(:enter)
end

Then /^I should see title "([^\"]*)"$/ do |title|
  expect(page).to have_title title
end
```

Once we have defined the test case, we are ready to integrate this Capybara test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

We can now integrate our Capybara test case into BrowserStack. Integration of Capybara with BrowserStack is made possible by use of following module:

```
require 'yaml'
require 'selenium/webdriver'
require 'capybara/cucumber'
require 'browserstack/local'

# monkey patch to avoid reset sessions
class Capybara::Selenium::Driver < Capybara::Driver::Base
  def reset!
    if @browser
      @browser.navigate.to('about:blank')
    end
  end
end

TASK_ID = (ENV['TASK_ID'] || 0).to_i
CONFIG_NAME = ENV['CONFIG_NAME'] || 'single'

CONFIG = YAML.load(File.read(File.join(File.dirname(__FILE__), "../../config/#{CONFIG_NAME}.config.yml")))
CONFIG['user'] = ENV['BROWSERSTACK_USERNAME'] || CONFIG['user']
CONFIG['key'] = ENV['BROWSERSTACK_ACCESS_KEY'] || CONFIG['key']


Capybara.register_driver :browserstack do |app|
  @caps = CONFIG['common_caps'].merge(CONFIG['browser_caps'][TASK_ID])

  # Code to start browserstack local before start of test
  if @caps['browserstack.local'] && @caps['browserstack.local'].to_s == 'true';
    @bs_local = BrowserStack::Local.new
    bs_local_args = {"key" => "#{CONFIG['key']}"}
    @bs_local.start(bs_local_args)
  end

  Capybara::Selenium::Driver.new(app,
    :browser => :remote,
    :url => "http://#{CONFIG['user']}:#{CONFIG['key']}@#{CONFIG['server']}/wd/hub",
    :desired_capabilities => @caps
  )
end

Capybara.default_driver = :browserstack

# Code to stop browserstack local after end of test
at_exit do
  @bs_local.stop unless @bs_local.nil?
end
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials.

```
server: "hub-cloud.browserstack.com"
user: "USERNAME"
key: "ACCESS_KEY"

common_caps:
  "browserstack.debug": true

browser_caps:
  -
    "browser": "chrome"
```

We are now ready to run the test on BrowserStack, using the following command:

```
# Run using rake
bundle exec rake single
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Capybara automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your Capybara tests for Local Testing takes just three steps:

1. Install local bindings:
```
# Install using gem
gem install browserstack-local
```

2. Next, you need to update your Capybara config file, and set the browserstack.local capability to true:

```
server: "hub-cloud.browserstack.com"
user: "USERNAME"
key: "ACCESS_KEY"

common_caps:
  "browserstack.local": true

browser_caps:
  -
    "browser": "chrome"
```
Here is a sample test case written for running local with Capybara.


```
# Local Feature
Feature: BrowserStack Local Testing

Background:
  Given I am on http://bs-local.com:45691/check

Scenario: Can check tunnel working
  Then I should see "Up and running"
```
```
# Local Steps
Given /^I am on (.*)$/ do |url|
  visit url
end

Then /^I should see "([^\"]*)"$/ do |body|
  expect(page).to have_text body
end
```

3. You can now run your Capybara test using BrowserStack Local with the following command:

```
# Run using rake
bundle exec rake local
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Capybara tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Capybara on BrowserStack, modify the config file as shown below:
```
server: "hub-cloud.browserstack.com"
user: "USERNAME"
key: "ACCESS_KEY"

common_caps:
  "browserstack.debug": true

browser_caps:
  -
    "browser": "chrome"
  -
    "browser": "firefox"
  -
    "browser": "internet explorer"
  -
    "browser": "safari"
```
Capabilities for each environment can be customised as explained earlier.

Run your tests in parallel on BrowserStack using following command:

```
# Run using rake
bundle exec rake parallel
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Capybara test cases.

```
require 'rest_client'
RestClient.put 'https://USERNAME:ACCESS_KEY@api.browserstack.com/automate/sessions/<session-id>.json', {"status"=>"completed", "reason"=>""}, {:content_type => :json}

```
The two potential values for status can either be completed or error. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/selenium/api-reference).


### Step 5: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Capybara tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
common_caps:
  "browserstack.debug": true
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
common_caps:
  "browserstack.video": false
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/ruby-capybara/debug-failed-tests)

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
