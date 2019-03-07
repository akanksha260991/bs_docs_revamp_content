# Integrate BrowserStack Automate with Travis CI

## Introduction
This add-on automatically sets up [BrowserStack Local Testing](https://www.browserstack.com/local-testing) which allows you to test your private servers, alongside public URLs, using the BrowserStack cloud. To do this it uses the BrowserStackLocal binary for your build platform.

BrowserStack Local Testing establishes a secure connection between your Travis build container / VM and BrowserStack servers. Local Testing also has support for firewalls, proxies and Active Directory. Once the secure connection is setup, all URLs work out of the box, including your webserver, local folders, as well as URLs with HTTPS.

## Setting up BrowserStack
Please sign up for a BrowserStack account if you haven't already; it's [free](https://www.browserstack.com/pricing) for open source projects. Once you have signed up get your Username and Access Key from the [account settings page](https://www.browserstack.com/accounts/settings). Your Username and Access Key are required to configure the `.travis.yml` file of your project.

Choose whether you want to store your Access Key as plain text or in a secure/encrypted form. For open source projects we recommend storing the Access Key in a secure form so that pull requests cannot use the keys stored in your `.travis.yml`. For more information see the [pull requests page on Travis CI](http://docs.travis-ci.com/user/pull-requests/#Security-Restrictions-when-testing-Pull-Requests).

### Encrypted Access Key
To encrypt your Access Key for use in `.travis.yml` you can use:

```bash
travis encrypt <your_browserstack_access_key>
```

**Note:** You need to have the Travis CLI installed to be able to do this (see [Encryption Keys](http://docs.travis-ci.com/user/encryption-keys/) for more details). Once your Access Key is encrypted, you can add the secure string to your `.travis.yml` file as follows:

```yml
addons:
  browserstack:
    username: "Your BrowserStack Username"
    access_key:
      secure: "The secure string output of travis encrypt"
```

### Plain Text Access Key
To store your Access Key in plain text format, add the following configuration to your `.travis.yml` file:

```yml
addons:
  browserstack:
    username: "Your BrowserStack Username"
    access_key: "Your BrowserStack Access Key"
```

**We strongly recommend** storing your BrowserStack Access Keys in encrypted format, since other users that have access to your repository can read and use your plain text Access Keys to test on BrowserStack.

### Local Identifier
A Local Identifier is a unique identifier for each Local connection when multiple Local connections are connected. The add-on will **always** create a Local Identifier for each local connection that is created. If you are using the Selenium testing framework, the Local Identifier must be added to the Selenium capabilities.

The Local Identifier is exposed as an environment variable `BROWSERSTACK_LOCAL_IDENTIFIER`. You can use it to set the Selenium capability.

See the following example which uses Ruby's [selenium-webdriver](https://www.browserstack.com/automate/ruby):

```ruby
require 'rubygems'
require 'selenium-webdriver'

# Input capabilities
caps = Selenium::WebDriver::Remote::Capabilities.new
caps['browserstack.local'] = 'true'
caps['browserstack.localIdentifier'] = ENV['BROWSERSTACK_LOCAL_IDENTIFIER']
# Add other capabilities like browser name, version and os name, version
...

driver = Selenium::WebDriver.for(:remote,
  :url => "http://sreyanthachary2:3q1JqpJpHRDTfvrcvyA4@hub-cloud.browserstack.com/wd/hub",
  :desired_capabilities => caps)
```

Local identifiers are essential for [matrix builds](https://docs.travis-ci.com/user/customizing-the-build/#Build-Matrix). Since matrix builds in Travis can be run on the same VM, we need to add the Local Identifier when starting the connection to ensure that the correct local tunnel gets the right requests.

## Additional Options
### Proxy
Local Testing also allows you to set the proxy host, port, username and password through which all URLs will be resolved:

```yml
addons:
  browserstack:
    username: "Your BrowserStack Username"
    access_key:
      secure: "The secure string output of `travis encrypt`"
    proxyHost: "Proxy server host"
    proxyPort: "Proxy server port"
    proxyUser: "User to use when accessing proxy server"
    proxyPass: "Password to use when accessing proxy server"
```

### More Options
Some other options that are supported by the add on -

* `forcelocal`: If this is set to true then all URLs will be resolved via the Travis container that your build is running in.
* `only`: restricts Local Testing access to the specified local servers and/or folders.

Sample usage:

```yml
addons:
  browserstack:
    username: "Your BrowserStack Username"
    access_key:
      secure: "The secure string output of `travis encrypt`"
    forcelocal: true
    only: dev.example.com,80,0,*.example.org,80,0
```

The format for the only flag is, "Host pattern","Host Port","Flag for SSL True(1)/False(0)" and repeat.

## Resources
[BrowserStack Documentation on Travis Docs](https://docs.travis-ci.com/user/browserstack/)
