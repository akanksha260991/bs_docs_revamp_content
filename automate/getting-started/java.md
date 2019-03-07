# Selenium with Java

#### Your guide to running Selenium Webdriver tests with Java on BrowserStack.
***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Java on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

```
Prerequisites:
Before you can start running your Selenium tests with Java:

1. Download the Selenium Java bindings from the Selenium website
2. Extract the ZIP file you downloaded from the URL above, and add the Selenium Java bindings (a JAR file) and all the dependent libraries (JAR files in the /libs folder) to your classpath.
```


***

### Step 1: Run your first test

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

To get started, let’s run a simple Selenium Webdriver test. The Java script below will open a URL, input a string, submit the form, and return the page title.

***First***, select the OS and Device/Browser combination you'd like to test on using the drop-down menus below. This will automatically update the Java code sample below:

<OS / Device Selector>

Look for the <real device> icon to select a real device.

> Note: Testing on real devices requires the [Automate Mobile plan](https://www.browserstack.com/pricing?tab=browser-plans-tab)

```
import org.openqa.selenium.By;
import org.openqa.selenium.Platform;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;

import java.net.URL;

public class JavaSample {

  public static final String USERNAME = "USERNAME";
  public static final String AUTOMATE_KEY = "ACCESS_KEY";
  public static final String URL = "https://" + USERNAME + ":" + AUTOMATE_KEY + "@hub-cloud.browserstack.com/wd/hub";

  public static void main(String[] args) throws Exception {

    DesiredCapabilities caps = new DesiredCapabilities();
    caps.setCapability("browserName", "iPhone");
    caps.setCapability("device", "iPhone 8 Plus");
    caps.setCapability("realMobile", "true");
    caps.setCapability("os_version", "11");

    WebDriver driver = new RemoteWebDriver(new URL(URL), caps);
    driver.get("http://www.google.com");
    WebElement element = driver.findElement(By.name("q"));

    element.sendKeys("BrowserStack");
    element.submit();

    System.out.println(driver.getTitle());
    driver.quit();

  }
}
```
> Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.

***Second***, copy-and-paste the code sample into your code editor, save it as a .java file, and execute the test from your command line.

***Third***, verify the results. The Selenium Webdriver test should have opened a URL, inputted a string, submitted the form, and returned the page title. Your results will be displayed on the command-line interface and on the [Automate dashboard](https://automate.browserstack.com), where you can see Text Logs, Screenshots of every Selenium command, and a Video Recording of your entire test.

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

In two steps, configure your Selenium Webdriver tests for Local Testing:

1. Download and run the BrowserStackLocal binary:

  Download the appropriate binary:

  - OS X (Recommended for your system)
  - Linux 32-bit
  - Linux 64-bit
  - Windows

  The download links are secure. The binaries are digitally signed, identifying the publisher as BrowserStack Ltd. Read more about our [security](https://www.browserstack.com/security#local-testing).

  Navigate to the folder containing the binary, and run it from the command line.

  <OS X & Linux / Windows selector>

  **OS X & Linux**
  ```
  ./BrowserStackLocal --key ekKTGeFduvsvXvXx96xG
  ```

  **Windows**
  ```
  BrowserStackLocal.exe --key ekKTGeFduvsvXvXx96xG
  ```
2. Once the connection is established, enable local testing in your tests by setting the browserstack.local capability to true.
```
caps.setCapability("browserstack.local", "true");
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Selenium Webdriver tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application.

With Parallel Testing, you can run the same test on different browser/device combinations, or run different tests on the same browser/device combination. Parallel Testing will help you reduce the run time of your test suite, resulting in faster build times and faster releases.

To start Parallel testing, you can use any of the popular test frameworks which work with Java and Selenium. We have provided getting started guides on some of the popular frameworks below:
- [TestNG](https://www.browserstack.com/automate/testng)
- [Gauge](https://www.browserstack.com/automate/gauge)
- [JBehave](https://www.browserstack.com/automate/jbehave)
- [JUnit](https://www.browserstack.com/automate/junit)
- [Selenide](https://www.browserstack.com/automate/selenide)
- [Serenity](https://www.browserstack.com/automate/serenity)

### Step 4: Mark tests as pass / fail
It is possible to mark tests as either a pass or a fail, using the following snippet:

```
import java.net.URI;
import java.net.URISyntaxException;
import java.util.ArrayList;
import java.io.IOException;
import java.io.UnsupportedEncodingException;

import org.apache.http.NameValuePair;
import org.apache.http.client.methods.HttpPut;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.client.entity.UrlEncodedFormEntity;

public static void mark() throws URISyntaxException, UnsupportedEncodingException, IOException {
  URI uri = new URI("https://USERNAME:ACCESS_KEY@api.browserstack.com/automate/sessions/<session-id>.json");
  HttpPut putRequest = new HttpPut(uri);

  ArrayList<NameValuePair> nameValuePairs = new ArrayList<NameValuePair>();
  nameValuePairs.add((new BasicNameValuePair("status", "passed")));
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
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Java script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

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
In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/java-testng/debug-failed-tests)

### Next steps
Congrats! You are now able to run your tests on BrowserStack cloud. You may want to check out these resources:
***
#### [Testing on internal networks](https://www.browserstack.com/docs/selenium/<language>/getting-started-with-local-testing)
***
#### [Run tests in parallel](https://www.browserstack.com/docs/selenium/<language>/getting-started-with-parallel-testing)
***
#### [Debug your app](https://www.browserstack.com/docs/selenium/<language>/view-test-results)
***
#### [Integrate with CICD Tools](https://www.browserstack.com/docs/selenium/<language>/jenkins)
***
### Questions?
We're always happy to help with any questions you might have! Read [FAQ](https://www.browserstack.com/support?tag=automate), [Contact support](https://www.browserstack.com/contact), or [connect with our sales team](https://www.browserstack.com/contact#sales).

```
Was this page helpful? Yes No
```
