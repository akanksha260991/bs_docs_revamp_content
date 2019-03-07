# Selenium with NUnit

#### Your guide to running Selenium Webdriver tests with NUnit on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with NUnit on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [nunit-browserstack](https://github.com/browserstack/nunit-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with NUnit, ensure you have the NUnit libraries installed:

```
Install NUnit and NUnit3TestAdapter using NuGet Gallery

```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in NUnit with C#
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample NUnit test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
[TestFixture("single", "chrome")]
public class SingleTest : BrowserStackNUnitTest
{
  public SingleTest(string profile, string environment) : base(profile, environment) { }

  [Test]
  public void SearchGoogle()
  {
    driver.Navigate().GoToUrl("https://www.google.com/ncr");
    IWebElement query = driver.FindElement(By.Name("q"));
    query.SendKeys("BrowserStack");
    query.Submit();
    System.Threading.Thread.Sleep(5000);
    Assert.AreEqual("BrowserStack - Google Search", driver.Title);
  }
}
```
Once we have defined the test case, we are ready to integrate this NUnit test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Integration of NUnit with BrowserStack is made possible by use of following module:

```
[TestFixture]
public class BrowserStackNUnitTest
{
  protected IWebDriver driver;
  protected string profile;
  protected string environment;
  private Local browserStackLocal;

  public BrowserStackNUnitTest(string profile, string environment)
  {
    this.profile = profile;
    this.environment = environment;
  }

  [SetUp]
  public void Init()
  {
    NameValueCollection caps = ConfigurationManager.GetSection("capabilities/" + profile) as NameValueCollection;
    NameValueCollection settings = ConfigurationManager.GetSection("environments/" + environment) as NameValueCollection;

    DesiredCapabilities capability = new DesiredCapabilities();

    foreach (string key in caps.AllKeys)
    {
      capability.SetCapability(key, caps[key]);
    }

    foreach (string key in settings.AllKeys)
    {
      capability.SetCapability(key, settings[key]);
    }

    String username = Environment.GetEnvironmentVariable("BROWSERSTACK_USERNAME");
    if(username == null)
    {
      username = ConfigurationManager.AppSettings.Get("user");
    }

    String accesskey = Environment.GetEnvironmentVariable("BROWSERSTACK_ACCESS_KEY");
    if (accesskey == null)
    {
      accesskey = ConfigurationManager.AppSettings.Get("key");
    }

    capability.SetCapability("browserstack.user", username);
    capability.SetCapability("browserstack.key", accesskey);

    String appId = Environment.GetEnvironmentVariable("BROWSERSTACK_APP_ID");
    if (appId != null)
    {
      capability.SetCapability("app", appId);
    }

    if (capability.GetCapability("browserstack.local") != null && capability.GetCapability("browserstack.local").ToString() == "true")
    {
      browserStackLocal = new Local();
      List<KeyValuePair<string, string>> bsLocalArgs = new List<KeyValuePair<string, string>>() {
        new KeyValuePair<string, string>("key", accesskey)
      };
      browserStackLocal.start(bsLocalArgs);
    }

    driver = new RemoteWebDriver(new Uri("http://"+ ConfigurationManager.AppSettings.Get("server") +"/wd/hub/"), capability);
  }

  [TearDown]
  public void Cleanup()
  {
    driver.Quit();
    if (browserStackLocal != null)
    {
      browserStackLocal.stop();
    }
  }
}
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="single" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>

    <sectionGroup name="environments">
      <section name="chrome" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
  </configSections>

  <appSettings>
    <add key="user" value="USERNAME" />
    <add key="key" value="ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <single>
      <add key="browserstack.debug" value="true" />
    </single>
  </capabilities>

  <environments>
    <chrome>
      <add key="browser" value="chrome" />  
    </chrome>
  </environments>
</configuration>
```
Run your test on BrowserStack using following command:

```
Build the solution in Visual Studio
Run test with fixture "single" from Test Explorer
```

### Step 2: Test on internal networks

BrowserStack enables you to run your NUnit automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your NUnit tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:
```
Install BrowserstackLocal using NuGet Gallery

```

2. Next, you need to update your NUnit config file, local.conf.json, and set the browserstack.local capability to true:

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>

    <sectionGroup name="environments">
      <section name="chrome" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
  </configSections>

  <appSettings>
    <add key="user" value="USERNAME" />
    <add key="key" value="ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <local>
      <add key="browserstack.local" value="true" />
    </local>
  </capabilities>

  <environments>
    <chrome>
      <add key="browser" value="chrome" />  
    </chrome>
  </environments>
</configuration>
```
Here is a sample test case written for running local with NUnit.


```
[TestFixture("local", "chrome")]
public class LocalTest : BrowserStackNUnitTest
{
  public LocalTest(string profile, string environment) : base(profile, environment) { }

  [Test]
  public void HealthCheck()
  {
    driver.Navigate().GoToUrl("http://bs-local.com:45691/check");
    Assert.IsTrue(Regex.IsMatch(driver.PageSource, "Up and running", RegexOptions.IgnoreCase));
  }
}
```
3. You can now run your NUnit test using BrowserStack Local with the following command:

```
Build the solution in Visual Studio
Run test with fixture "local" from Test Explorer
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple NUnit tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with NUnit on BrowserStack, modify the parallel.conf.js config file as shown below:
```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="parallel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>

    <sectionGroup name="environments">
      <section name="chrome" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="firefox" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="safari" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="ie" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
  </configSections>

  <appSettings>
    <add key="user" value="USERNAME" />
    <add key="key" value="ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <parallel>
      <add key="browserstack.debug" value="true" />
    </parallel>
  </capabilities>

  <environments>
    <chrome>
      <add key="browser" value="chrome" />  
    </chrome>
    <firefox>
      <add key="browser" value="firefox" />  
    </firefox>
    <safari>
      <add key="browser" value="safari" />  
    </safari>
    <ie>
      <add key="browser" value="ie" />  
    </ie>
  </environments>
</configuration>
```
Capabilities for each environment can be customised as explained earlier.

Parallel test cases need to be written for running in parallel with NUnit.

```
[TestFixture("parallel", "chrome")]
[TestFixture("parallel", "firefox")]
[TestFixture("parallel", "safari")]
[TestFixture("parallel", "ie")]
[Parallelizable(ParallelScope.Fixtures)]
public class ParallelTest : SingleTest
{
  public ParallelTest(string profile, string environment) : base(profile, environment) { }
}
```

You can now run your tests in parallel on BrowserStack using the following command:
```
Build the solution in Visual Studio
Run tests with fixture "parallel" from Test Explorer
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your NUnit test cases.

```
namespace RestApi {
  class ChangeSessionStatus {
    static void Main(string[] args) {
      string reqString = "{\"status\":\"completed\", \"reason\":\"\"}";

      byte[] requestData = Encoding.UTF8.GetBytes(reqString);
      Uri myUri = new Uri(string.Format("https://www.browserstack.com/automate/sessions/<session-id>.json"));
      WebRequest myWebRequest = HttpWebRequest.Create(myUri);
      HttpWebRequest myHttpWebRequest = (HttpWebRequest)myWebRequest;
      myWebRequest.ContentType = "application/json";
      myWebRequest.Method = "PUT";
      myWebRequest.ContentLength = requestData.Length;
      using (Stream st = myWebRequest.GetRequestStream())st.Write(requestData, 0, requestData.Length);

      NetworkCredential myNetworkCredential = new NetworkCredential("USERNAME", "ACCESS_KEY");
      CredentialCache myCredentialCache = new CredentialCache();
      myCredentialCache.Add(myUri, "Basic", myNetworkCredential);
      myHttpWebRequest.PreAuthenticate = true;
      myHttpWebRequest.Credentials = myCredentialCache;

      myWebRequest.GetResponse().Close();
    }
  }
}
```
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/selenium/api-reference).


### Step 5: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your NUnit tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
<capabilities>
  <single>
    <add key="browserstack.debug" value="true" />
  </single>
</capabilities>
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
<capabilities>
  <single>
    <add key="browserstack.video" value="false" />
  </single>
</capabilities>
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/c-sharp-nunit/debug-failed-tests)

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
