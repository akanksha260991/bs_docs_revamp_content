# Integrate BrowserStack Automate with BitBucket Pipeline

## Introduction
[Bitbucket Pipelines](https://bitbucket.org/product/features/pipelines?utm_source=browserstack&utm_medium=press-release&utm_campaign=bitbucket_bitbucket-pipelines) uses `bitbucket-pipelines.yml` for configuration, and it can be used to integrate your tests with BrowserStack.

## Configuration

1. Go to your Bitbucket repository's settings and add the following Environment variables:

  `BROWSERSTACK_USER`: Your BrowserStack username.

  `BROWSERSTACK_ACCESSKEY`: Your BrowserStack access key.

2. Configure `bitbucket-pipleines.yml`. Example configuration is below:

```yml
image: ruby:2.1
pipelines:
  default:
    - step:
        script:
          - apt-get update
          - apt-get install unzip
          - bundle install
          - bundle exec rackup -p 8001 > /dev/null &
          - ./run_local.bash #This file contains the steps to download and run BrowserStack Local
          - bundle exec rspec spec/welcome.rb
```

`run_local.bash` file downloads and starts [BrowserStack Local](https://www.browserstack.com/local-testing#command-line)

```bash
#!/bin/bash -e

wget http://www.browserstack.com/browserstack-local/BrowserStackLocal-linux-x64.zip
unzip BrowserStackLocal-linux-x64.zip
./BrowserStackLocal ${BROWSERSTACK_ACCESSKEY} > /dev/null &
sleep 10
```

3. Read Bitbucket environment variables in your tests. The following example demonstrates it with RSpec.

```ruby
require 'selenium-webdriver'
require 'rspec'

RSpec.configure do |config| config.color_enabled = true end

describe "The welcome demo" do

  before(:all) do
    browserstack_user = ENV['BROWSERSTACK_USER']
    browserstack_key = ENV['BROWSERSTACK_ACCESSKEY']
    caps = Selenium::WebDriver::Remote::Capabilities.new
    caps['os'] = 'windows'
    caps['os_version'] = '7'
    caps['browser'] = 'chrome'
    caps['browserstack.local'] = true
    caps['browserstack.debug'] = true
    caps['build'] = 'Bitbucket Pipelines'
    caps['name'] =  'Test'
    @driver = Selenium::WebDriver.for(:remote, :url => "http://#{browserstack_user}:#{browserstack_key}@hub-cloud.browserstack.com/wd/hub", :desired_capabilities => caps)
 end

  after(:all) do
    @driver.quit
  end

  it "should have a welcome page" do
    username = 'Test User'
    @driver.get 'http://localhost:8001'
    @driver.find_element(:name ,'name').send_keys username
    @driver.find_element(:css, "input[type='submit']").click
    actual_text = @driver.find_element(:css, "h2").text
    actual_text.should == "Welcome, #{username}"
  end

end
```

For the complete working example of how to configure Bitbucket Pipelines for BrowserStack, check out this [repository](https://bitbucket.org/browserstack/bitbucket-pipelines).
