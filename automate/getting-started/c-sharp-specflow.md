# Selenium with SpecFlow

#### Your guide to running Selenium Webdriver tests with SpecFlow on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with SpecFlow on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [specflow-browserstack](https://github.com/browserstack/specflow-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with SpecFlow:

1. Ensure you have Microsoft Visual Studio 2015 Update 2 or above installed and running on your machine
2. [Install SpecFlow](https://www.nuget.org/packages/SpecFlow/) using the NuGet Gallery

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in SpecFlow with C#
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample SpecFlow test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
// Google Feature
Feature: Google

Scenario Outline: Can find search results
  Given I am on the google page for <profile> and <environment>
  When I search for "BrowserStack"
  Then I should see title "BrowserStack - Google Search"

  Examples:
    | profile | environment |
    | single    | chrome      |
```
Once we have defined the feature file, which contains the test case, we now need to create the step definition. The step definition for the SpecFlow test case shown above is:

```
// Google Steps
[Binding]
public class SingleSteps
{
  private IWebDriver _driver;
  readonly BrowserStackDriver _bsDriver;

  public SingleSteps()
  {
    _bsDriver = (BrowserStackDriver)ScenarioContext.Current["bsDriver"];
  }

  [Given(@"I am on the google page for (.*) and (.*)")]
  public void GivenIAmOnTheGooglePage(string profile, string environment)
  {
    _driver = _bsDriver.Init(profile, environment);
    _driver.Navigate().GoToUrl("https://www.google.com/ncr");
  }

  [When(@"I search for ""(.*)""")]
  public void WhenISearchFor(string keyword)
  {
    var q = _driver.FindElement(By.Name("q"));
    q.SendKeys(keyword);
    q.Submit();
  }

  [Then(@"I should see title ""(.*)""")]
  public void ThenIShouldSeeTitle(string title)
  {
    Thread.Sleep(5000);
    Assert.That(_driver.Title, Is.EqualTo(title));
  }
}
```
Now that we have created the feature file and the step definition, we are ready to integrate this SpecFlow test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Now that we have created a feature file and step definitions, we can integrate our SpecFlow test case into BrowserStack. To start, we define two classes which contain methods to configure and create the connection with BrowserStack.

```
[Binding]
public sealed class BrowserStack
{
  private BrowserStackDriver bsDriver;
  private string[] tags;

  [BeforeScenario]
  public void BeforeScenario()
  {
    bsDriver = new BrowserStackDriver(ScenarioContext.Current);
    ScenarioContext.Current["bsDriver"] = bsDriver;
  }

  [AfterScenario]
  public void AfterScenario()
  {
    bsDriver.Cleanup();
  }
}
```

```
public class BrowserStackDriver
{
  private IWebDriver driver;
  private Local browserStackLocal;
  private string profile;
  private string environment;
  private ScenarioContext context;

  public BrowserStackDriver(ScenarioContext context)
  {
    this.context = context;
  }

  public IWebDriver Init(string profile, string environment)
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
    if (username == null)
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

    File.AppendAllText("C:\\Users\\Admin\\Desktop\\sf.log", "Starting local");

    if (capability.GetCapability("browserstack.local") != null && capability.GetCapability("browserstack.local").ToString() == "true")
    {
      browserStackLocal = new Local();
      List<KeyValuePair<string, string>> bsLocalArgs = new List<KeyValuePair<string, string>>() {
        new KeyValuePair<string, string>("key", accesskey)
      };
      browserStackLocal.start(bsLocalArgs);
    }

    File.AppendAllText("C:\\Users\\Admin\\Desktop\\sf.log", "Starting driver");
    driver = new RemoteWebDriver(new Uri("http://" + ConfigurationManager.AppSettings.Get("server") + "/wd/hub/"), capability);
    return driver;
  }

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

We now need to create a .config file, which contains the BrowserStack Hub URL and credentials required to connect to the BrowserStack Selenium grid.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <section name="specFlow" type="TechTalk.SpecFlow.Configuration.ConfigurationSectionHandler, TechTalk.SpecFlow" />

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

<startup><supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.1" /></startup><specFlow>
    <!-- For additional details on SpecFlow configuration options see http://go.specflow.org/doc-config -->
  <!-- For additional details on SpecFlow configuration options see http://go.specflow.org/doc-config --><unitTestProvider name="NUnit" /></specFlow></configuration>
```

We are now ready to run the test on BrowserStack, using the following command:

```
Build the solution in Visual Studio 2015 Update 2
Run test with fixture "single" from Test Explorer
```

### Step 2: Test on internal networks

BrowserStack enables you to run your SpecFlow automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your SpecFlow tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:
```
Install BrowserstackLocal using NuGet Gallery

```

2. Next, you need to update your SpecFlow config file, local.conf.json, and set the browserstack.local capability to true:

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <section name="specFlow" type="TechTalk.SpecFlow.Configuration.ConfigurationSectionHandler, TechTalk.SpecFlow" />

    <sectionGroup name="capabilities">
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>

    <sectionGroup name="environments">
      <section name="chrome" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
  </configSections>

  <appSettings>
    <add key="user" value="BROWSERSTACK_USERNAME" />
    <add key="key" value="BROWSERSTACK_ACCESS_KEY" />
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

<startup><supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.1" /></startup><specFlow>
    <!-- For additional details on SpecFlow configuration options see http://go.specflow.org/doc-config -->
  <!-- For additional details on SpecFlow configuration options see http://go.specflow.org/doc-config --><unitTestProvider name="NUnit" /></specFlow></configuration>
```
Here is a sample feature and steps file which using Local testing:


```
// Local Feature
Feature: Local

Scenario Outline: Can check tunnel working
  Given I opened health check for <profile> and <environment>
  Then I should see "Up and running"

  Examples:
    | profile | environment |
    | local   | chrome      |
```

```
// Local Steps
[Binding]
public class LocalSteps
{
  private IWebDriver _driver;
  readonly BrowserStackDriver _bsDriver;

  public LocalSteps()
  {
    _bsDriver = (BrowserStackDriver)ScenarioContext.Current["bsDriver"];
  }

  [Given(@"I opened health check for (.*) and (.*)")]
  public void GivenIOpenedHealthCheck(string profile, string environment)
  {
    _driver = _bsDriver.Init(profile, environment);
    _driver.Navigate().GoToUrl("http://bs-local.com:45691/check");
  }

  [Then(@"I should see ""(.*)""")]
  public void ThenIShouldSee(string body)
  {
    Assert.That(_driver.PageSource, Does.Contain(body));
  }
}

```
3. You can now run your SpecFlow test using BrowserStack Local with the following command:

```
Build the solution in Visual Studio 2015 Update 2
Run test with fixture "local" from Test Explorer
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple SpecFlow tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with SpecFlow on BrowserStack, modify the config file as below:

```
// App.config
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <section name="specFlow" type="TechTalk.SpecFlow.Configuration.ConfigurationSectionHandler, TechTalk.SpecFlow" />

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

<startup><supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.1" /></startup><specFlow>
    <!-- For additional details on SpecFlow configuration options see http://go.specflow.org/doc-config -->
  <!-- For additional details on SpecFlow configuration options see http://go.specflow.org/doc-config --><unitTestProvider name="NUnit" /></specFlow></configuration>
```

```
// Add to AssemblyInfo.cs
[assembly: Parallelizable(ParallelScope.Fixtures)]
```

Capabilities for each environment can be customised as explained earlier.

Parallel test cases need to be written for running in parallel with SpecFlow.

```
// Google Feature for parallel run
Feature: Google

Scenario Outline: Can find search results
  Given I am on the google page for <profile> and <environment>
  When I search for "BrowserStack"
  Then I should see title "BrowserStack - Google Search"

  Examples:
    | profile | environment |
    | parallel  | chrome      |
    | parallel  | firefox     |
    | parallel  | safari      |
    | parallel  | ie          |
```
You can now run your tests in parallel on BrowserStack using the following command:

```
Build the solution in Visual Studio 2015 Update 2
Run tests with fixture "parallel" from Test Explorer
```

> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your SpecFlow test cases.

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
Visual Logs automatically capture the screenshots generated at every Selenium command run through your SpecFlow tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

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

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/c-sharp-specflow/debug-failed-tests)

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
