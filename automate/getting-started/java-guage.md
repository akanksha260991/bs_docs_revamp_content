# Selenium with Gauge

#### Documentation for writing automate test scripts for Gauge framework in Java.
***
> Note: Refer our sample repo on Github: [gauge-java-browserstack](https://github.com/browserstack/gauge-java-browserstack)


Running your Gauge tests on our cloud setup is simple and straightforward. For the code to run successfully on your machine, please ensure the following

* You have a [BrowserStack Automate] (https://automate.browserstack.com/) account with at least 4 parallel tests. Signup for a free trial [here](https://www.browserstack.com/users/sign_up).
* [Gauge](http://getgauge.io/) should be installed and in $PATH. Latest version of Gauge can be downloaded from [the website](http://getgauge.io/get-started/index.html).
* [Maven](http://maven.apache.org/) should be installed and in $PATH. Latest version of Maven can be downloaded from [the website](http://maven.apache.org/install.html).
* [Gauge-maven-plugin](http://getgauge.io/documentation/user/current/advanced_readings/dependency_management/maven-plugin.html) to execute specs manage dependencies using Maven.

Get started with a sample test, which opens Google's homepage, searches for BrowserStack, and asserts for the title of the search results page

```
Search
======

The following scenario performs a Google search and makes sure that
the results match.

Search for BrowserStack
-------------------
tags: search, smoke

* On the homepage
* Search for term "BrowserStack"
* Make sure the page title is "BrowserStack - Google Search"
```
```
package com.browserstack.gauge.pages;

import static org.junit.Assert.assertEquals;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;

import com.thoughtworks.gauge.Step;

public class SearchSpec {
    private final WebDriver driver;
    private WebElement element;

    public SearchSpec() {
        this.driver = DriverFactory.getDriver();
    }

    @Step("On the homepage")
    public void navigateToHomePage() {
        driver.get("https://www.google.com");
    }

    @Step("Search for term <term>")
    public void searchFor(String term) {
        element = driver.findElement(By.name("q"));
        element.sendKeys(term);
        element.submit();
    }

    @Step("Make sure the page title is <term>")
    public void checkPageTitle(String term) {
        assertEquals(term, driver.getTitle());
    }
}
```
To actually run the test case, we need to integrate with BrowserStack as follows.

***

### Integration with BrowserStack

> Note: Running your Gauge tests on BrowserStack requires a username and an access key.

Integrating your Gauge tests with BrowserStack involves updating the .properties files with your [BrowserStack Username and Access Key](https://www.browserstack.com/accounts/settings)

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

```
# default.properties
# The credentials associated with your BrowserStack account.
BROWSERSTACK_USERNAME = USERNAME
BROWSERSTACK_ACCESS_KEY = ACCESS_KEY
```
Additionally, you will need to create a remote webdriver that points to the BrowserStack hub and sets the capabilities based on the .properties files

```
package com.browserstack.gauge.pages;

import com.thoughtworks.gauge.AfterSpec;
import com.thoughtworks.gauge.BeforeSpec;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;

import java.net.MalformedURLException;
import java.net.URL;
import java.util.concurrent.TimeUnit;

public class DriverFactory {
    private static final String USERNAME = System.getenv("BROWSERSTACK_USERNAME");
    private static final String AUTOMATE_KEY = System.getenv("BROWSERSTACK_ACCESS_KEY");
    private static final String URL = "https://" + USERNAME + ":" + AUTOMATE_KEY + "@hub-cloud.browserstack.com/wd/hub";

    private static WebDriver driver;

    public static WebDriver getDriver() {
        return driver;
    }

    @BeforeSpec
    public void setUp() {
        try {
            DesiredCapabilities caps = new DesiredCapabilities();

            // Capabilities from environment
            caps.setCapability("browser", System.getenv("BROWSER"));
            caps.setCapability("browser_version", System.getenv("BROWSER_VERSION"));
            caps.setCapability("build", System.getenv("BUILD"));
            caps.setCapability("os", System.getenv("OS"));
            caps.setCapability("os_version", System.getenv("OS_VERSION"));

            // Hardcoded capabilities
            caps.setCapability("browserstack.debug", "true");

            URL remoteURL = new URL(URL);

            driver = new RemoteWebDriver(remoteURL, caps);
            driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);

        } catch (MalformedURLException e) {

            System.out.println(e.toString());

        }
    }

    @AfterSpec
    public void tearDown() {
        driver.close();
        driver.quit();
    }
}
```
Run your test on BrowserStack using the following command:

```
mvn test
```

### Mark tests as pass / fail
It is possible to mark tests as either a pass or a fail, using the following snippet:

```
public static void mark() throws URISyntaxException, UnsupportedEncodingException, IOException {
  URI uri = new URI("https://USERNAME:ACCESS_KEY@api.browserstack.com/automate/sessions/<session-id>.json");
  HttpPut putRequest = new HttpPut(uri);

  ArrayList<NameValuePair> nameValuePairs = new ArrayList<NameValuePair>();
  nameValuePairs.add((new BasicNameValuePair("status", "completed")));
  nameValuePairs.add((new BasicNameValuePair("reason", "")));
  putRequest.setEntity(new UrlEncodedFormEntity(nameValuePairs));

  HttpClientBuilder.create().build().execute(putRequest);
}
```
The two potential values for status can either be passed or failed. Optionally, a reason can also be passed.

### Step 4: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Gauge script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
caps.setCapability("browserstack.debug", "true");
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
caps.setCapability("browserstack.video", "false");
```
**Console Logs**
Console Logs capture the browser's console output at various steps of the test to troubleshoot javascript issues. You can retrieve Console Logs using our [REST API](https://www.browserstack.com/docs/selenium/api-reference). You will also be able to download logs from Automate Dashboard.

Console Logs are enabled with log level set to 'errors' by default. To set different log levels, you need to use the capability browserstack.console with values 'disable', 'errors', 'warnings', 'info' or 'verbose', as shown below:
```
caps.setCapability("browserstack.console", "warnings");
```
**Network Logs**
Network Logs capture the browser's performance data such as network traffic, latency, HTTP requests and responses in the HAR format. You can download network logs using the REST API or from the Automate Dashboard. You can visualize HAR files using the HAR Viewer.

Network Logs are disabled by default. To enable Network Logs use the capability **browserstack.networkLogs** with the value **'true'**, as shown below:
```
caps.setCapability("browserstack.networkLogs", "true");
```

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
