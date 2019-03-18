# Integrate BrowserStack Automate with Circle CI

## Introduction
CircleCI uses `.circleci/config.yml` for configuration (read more at [https://circleci.com/docs/2.0/getting-started/](https://circleci.com/docs/2.0/getting-started/)), and it can be used to integrate your tests with BrowserStack. There are some variables you need to set: **grid config**, **username**, **access key** etc. For more detailed information on setting environment variables in CircleCI, please refer to their [documentation](https://circleci.com/docs/environment-variables).

### Setting the variables
BrowserStack recommends you encrypt variables like **username** and **access** key. CircleCI enables the user to secure environment variables via their web interface. This functionality allows you to keep the contents of the secure variable private.

The key-value pairs are set in the project settings, which can be found under **Tweaks**, in the **Environment variables** section.
Public environment variables can be set directly via `circle.yml` in **machine** section, under **environment**.

Shown below is a sample configuration for the `circle.yml` file for Cucumber:

```yml
version: 2
jobs:
  build:
    machine:
      ruby:
        version: 2.0.0-p0
      environment:
        SELENIUM_HOST: hub.browserstack.com
        BS_AUTOMATE_PROJECT: "Run on circleCI"
        BS_AUTOMATE_BUILD: "build No. $CIRCLE_BUILD_NUM for circleCI"
        SELENIUM_PLATFORM: WINDOWS
        BS_AUTOMATE_OS_VERSION: 10
        SELENIUM_BROWSER: IE
        SELENIUM_VERSION: 11
    steps:
      - checkout
      - run:
         name: Move to cucumber/ and Run the tests.
         command: cd cucumber/ && bundle install && bundle exec cucumber

```

The example demonstrates how to read CircleCI environment variables from within Ruby’s testing framework, Cucumber.

```ruby
require 'selenium/webdriver'

url = "http://#{ENV['BS_USERNAME']}:#{ENV['BS_AUTHKEY']}@hub-cloud.browserstack.com/wd/hub"

capabilities = Selenium::WebDriver::Remote::Capabilities.new

capabilities['project'] = ENV['BS_AUTOMATE_PROJECT'] if ENV['BS_AUTOMATE_PROJECT']
capabilities['build'] = ENV['BS_AUTOMATE_BUILD'] if ENV['BS_AUTOMATE_BUILD']

capabilities['platform'] = ENV['SELENIUM_PLATFORM'] || 'ANY'
capabilities['browser'] = ENV['SELENIUM_BROWSER'] || 'chrome'
capabilities['browser_version'] = ENV['SELENIUM_VERSION'] if ENV['SELENIUM_VERSION']

browser = Selenium::WebDriver.for(:remote, :url => url, :desired_capabilities => capabilities)

Before do |scenario|
@browser = browser
end

at_exit do
browser.quit
end
```

### Running Local Tests
If you are testing in your development or staging environment, you can configure CircleCI to automatically download the latest version of the BrowserStack Local binary and instantiate the binary before your test starts. In addition to the code below, you will also have to enable local in your test script by setting the capability `browserstack.local` to `true`.

Refer to the sample code below:

```yml
machine:
  ruby:
    version: 2.0.0-p0
  environment:
    SELENIUM_HOST: hub-cloud.browserstack.com
    SELENIUM_PORT: 80
    BS_AUTOMATE_PROJECT: "Run on circleCI"
    BS_AUTOMATE_BUILD: "build No. $CIRCLE_BUILD_NUM for circleCI"
    SELENIUM_PLATFORM: WINDOWS
    SELENIUM_BROWSER: IE
    SELENIUM_VERSION: 9

test:
    pre:
    # Download the browserstack binary file
    - wget "https://www.browserstack.com/browserstack-local/BrowserStackLocal-linux-x64.zip"
    # Unzip the browserstack binary file
    - unzip BrowserStackLocal-linux-x64.zip
    # Run the file with your access key
    - ./BrowserStackLocal $ACCESS_KEY:
        background: true
    override:
    - bundle exec cucumber
```

For the complete working example of how to configure Circle CI, check out the [circle.yml file in our repository](https://github.com/browserstack/automate-ruby-samples/blob/master/cucumber/circle.yml).

Red Badger has also published a [blog post](http://red-badger.com/blog/2014/05/27/automated-cross-browser-testing-with-browserstack-and-circleci/) about BrowserStack and CircleCI.
