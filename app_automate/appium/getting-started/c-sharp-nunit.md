# Appium with NUnit

#### Your guide to running mobile app tests using Appium with NUnit on BrowserStack real device cloud.
***
BrowserStack gives you instant access to 2000+ real devices. Running your Appium test automation with NUnit for native and hybrid mobile apps on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

***

### Step 1: Run your first test

> Note: Running your Appium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Follow 3 easy steps to get started with your first Appium test on BrowserStack cloud.

#### 1. Setup environment
Ensure you have NUnit libraries installed.
```
Install NUnit and NUnit3TestAdapter using NuGet Gallery

```

#### 2. Upload your app
Upload your Android app (.apk file) or iOS app (.ipa file) to the BrowserStack servers using the REST API.

>Note: Note: If you do not have an .apk or .ipa file and are looking to simply try App Automate, you can download our [Android sample app](https://www.browserstack.com/app-automate/sample-apps/android/WikipediaSample.apk) or [iOS sample app](https://www.browserstack.com/app-automate/sample-apps/ios/BStackSampleApp.ipa) and upload to the BrowserStack servers using the above API. Complete list of REST APIs can be found [here](https://www.browserstack.com//app-automate/rest-api).

```
curl -u "USERNAME:ACCESS_KEY" \
-X POST "https://api-cloud.browserstack.com/app-automate/upload" \
-F "file=@/path/to/app/file/Application-debug.apk"
```

Please note the App URL (bs://<hashed appid>) returned in the response of this call:

```
{"app_url":"bs://<hashed appid>"}

```
> Note:
1. App upload will take few seconds to about a minute depending on the size of your app. Do not interrupt the curl command until you get the response back.
2. If you upload an iOS app, we will resign the app with our own provisioning profile to be able to install your app on our devices during test execution.
3. We will delete the uploaded app after 30 days from the date of upload.

Different ways you can upload your app can be found [here](https://www.browserstack.com/docs/app-automate/appium/c-sharp-nunit/upload-app).

#### 3. Configure and run test
Copy sample code provided in NUnit for Android and iOS. Update the desired capability "app" with the App URL returned from the above API call.

<Android/iOS selector>
##### Android (Toggle)

If you are using our Sample App, the sample test below will install the Sample App (Wikipedia App) on the device, search for 'browserstack' and asserts for the list of results. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for NUnit on Github: [nunit-appium-app-browserstack](https://github.com/browserstack/nunit-appium-app-browserstack)

```
namespace android
{
  [TestFixture("single","galaxy-s6")]
  public class SingleTest : BrowserStackNUnitTest
  {
    public SingleTest(string profile, string device) : base(profile,device){}

    [Test]
    public void searchWikipedia()
    {
      AndroidElement searchElement = (AndroidElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("Search Wikipedia")));
      searchElement.Click();
      AndroidElement insertTextElement = (AndroidElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(By.Id("org.wikipedia.alpha:id/search_src_text")));
      insertTextElement.SendKeys("BrowserStack");
      Thread.Sleep(5000);

      ReadOnlyCollection<AndroidElement> allProductsName = driver.FindElements(By.ClassName("android.widget.TextView"));
      Assert.True(allProductsName.Count > 0);
    }
  }
}
```
> Note:
> 1. UIAutomator2 is the default automation engine for Android.
> 2. Pass automationName as 'Appium' if you are using Appium automation engine
> 3. If your app uses webview, set the webview debugging flag to true when creating the webview object as described in the [Android remote debugging docs](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews): WebView.setWebContentsDebuggingEnabled(true);

##### iOS (Toggle)
If you are using our iOS Sample App, the sample test below will install the Sample App (WordPress App) on the device, navigate to the Login screen, enters the login email and check whether the email is registered on WordPress. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for NUnit on Github: [nunit-appium-app-browserstack](https://github.com/browserstack/nunit-appium-app-browserstack)

```
namespace ios
{
    [TestFixture("single", "iphone-7")]
    public class SingleTest : BrowserStackNUnitTest
    {
        public SingleTest(string profile, string environment) : base(profile, environment) { }

        [Test]
        public void textVerificationTest()
        {
            IOSElement textButton = (IOSElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("Text Button")));
            textButton.Click();

            IOSElement textInput = (IOSElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("Text Input")));
            textInput.SendKeys("hello@browserstack.com"+"\n");

            IOSElement textOutput = (IOSElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("Text Output")));
            Assert.Equals(textOutput.Text,"hello@browserstack.com");
        }
    }
}
```

>Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.

To actually run the test case, we need to integrate with BrowserStack as follows.

##### Integration with BrowserStack
Integration of NUnit with BrowserStack is made possible by use of following module:

```
namespace android
{
    public class BrowserStackNUnitTest
    {
        protected AndroidDriver<AndroidElement> driver;
        protected string profile;
        protected string device;
        private Local browserStackLocal;

        public BrowserStackNUnitTest(string profile, string device)
        {
                this.profile = profile;
                this.device = device;
        }

        [SetUp]
        public void Init()
        {
            NameValueCollection caps = ConfigurationManager.GetSection("capabilities/" + profile) as NameValueCollection;
            NameValueCollection devices = ConfigurationManager.GetSection("environments/" + device) as NameValueCollection;

            DesiredCapabilities capability = new DesiredCapabilities();

            foreach (string key in caps.AllKeys)
            {
                capability.SetCapability(key, caps[key]);
            }

            foreach (string key in devices.AllKeys)
            {
                capability.SetCapability(key, devices[key]);
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

            driver = new AndroidDriver<AndroidElement>(new Uri("http://" + ConfigurationManager.AppSettings.Get("server") + "/wd/hub/"), capability);
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
}
```

The module reads from config file where you need to put the BrowserStack Hub URL and credentials. If the BROWSERSTACK_USERNAME and BROWSERSTACK_ACCESS_KEY are set in Environment variables, then that will take a preference. Also, update the App URL after uploading App to the BrowserStack cloud.


<Android / IOS Selector>
###### Android (Toggle)
```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="single" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="parallel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
    <sectionGroup name="environments">
      <section name="galaxy-s6" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="galaxy-s7" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />  
      <section name="pixel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>

  </configSections>

  <appSettings>
    <add key="user" value="BROWSERSTACK_USERNAME" />
    <add key="key" value="BROWSERSTACK_ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <single>
      <add key="build" value="nunit-browserstack" />
      <add key="name" value="single_test" />
      <add key="browserstack.debug" value="true" />
      <add key="app" value="bs://hashed-app-id"/>
    </single>
  </capabilities>

  <environments>
        <galaxy-s6>
            <add key="device" value="Samsung Galaxy S6"/>
        </galaxy-s6>
        <galaxy-s7>
            <add key="device" value="Samsung Galaxy S7"/>
        </galaxy-s7>
        <pixel>
            <add key="device" value="Google Pixel"/>
        </pixel>
    </environments>
</configuration>
```

###### iOS (Toggle)
```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="single" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="parallel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
    <sectionGroup name="environments">
      <section name="iphone-7" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="iphone-7-plus" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
  </configSections>

  <appSettings>
    <add key="user" value="BROWSERSTACK_USERNAME" />
    <add key="key" value="BROWSERSTACK_ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <single>
      <add key="build" value="nunit-browserstack" />
      <add key="name" value="single_test" />
      <add key="browserstack.debug" value="true" />
      <add key="app" value="bs://hashed-app-id"/>
    </single>
  </capabilities>

  <environments>
    <iphone-7>
      <add key="device" value="iPhone 7"/>
    </iphone-7>
    <iphone-7-plus>
      <add key="device" value="iPhone 7 Plus"/>
    </iphone-7-plus>
  </environments>

</configuration>
```

Run your test on BrowserStack using following command:

```
Build the solution in Visual Studio
Run test with fixture "single" from Test Explorer
```

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

>Note:  In order to try a local test, upload our Local Sample App for [Android](https://www.browserstack.com/app-automate/sample-apps/android/LocalSample.apk) and [iOS](https://www.browserstack.com/app-automate/sample-apps/ios/LocalSample.ipa)

Configuring your NUnit tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:

```
Install BrowserstackLocal using NuGet Gallery

```
2. Next, you need to update your NUnit config file, and set the browserstack.local capability to true:

<Android / IOS Selector>
###### Android (Toggle)
```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="single" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="parallel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
    <sectionGroup name="environments">
      <section name="galaxy-s6" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="galaxy-s7" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />  
      <section name="pixel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>

  </configSections>

  <appSettings>
    <add key="user" value="BROWSERSTACK_USERNAME" />
    <add key="key" value="BROWSERSTACK_ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <local>
      <add key="build" value="nunit-browserstack" />
      <add key="name" value="local_test" />
      <add key="browserstack.debug" value="true" />
      <add key="browserstack.local" value="true" />
      <add key="app" value="bs://hashed-app-id"/>
    </local>
  </capabilities>

  <environments>
        <galaxy-s6>
            <add key="device" value="Samsung Galaxy S6"/>
        </galaxy-s6>
        <galaxy-s7>
            <add key="device" value="Samsung Galaxy S7"/>
        </galaxy-s7>
        <pixel>
            <add key="device" value="Google Pixel"/>
        </pixel>
    </environments>
</configuration>
```

Here is a sample test case written for running local with NUnit.

```
namespace android
{
    [TestFixture("local", "galaxy-s7")]
    public class LocalTest : BrowserStackNUnitTest
    {
        public LocalTest(string profile, string device) : base(profile,device){ }

        [Test]
        public void testLocal()
        {
            AndroidElement searchElement = (AndroidElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(By.Id("com.example.android.basicnetworking:id/test_action")));
            searchElement.Click();
            AndroidElement testElement = (AndroidElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(By.ClassName("android.widget.TextView")));

            ReadOnlyCollection<AndroidElement> allTextViewElements = driver.FindElements(By.ClassName("android.widget.TextView"));

            Thread.Sleep(5000);

            foreach (AndroidElement textElement in allTextViewElements)
            {
                if (textElement.Text.Contains("The active connection is"))
                {
                    Assert.True(textElement.Text.Contains("The active connection is wifi"),"Incorrect Text");       
                }
            }
        }
    }
}
```
###### iOS (Toggle)
```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="single" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="parallel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
    <sectionGroup name="environments">
      <section name="iphone-7" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="iphone-7-plus" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
  </configSections>

  <appSettings>
    <add key="user" value="BROWSERSTACK_USERNAME" />
    <add key="key" value="BROWSERSTACK_ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <local>
      <add key="build" value="nunit-browserstack" />
      <add key="name" value="local_test" />
      <add key="browserstack.debug" value="true" />
      <add key="browserstack.local" value="true" />
      <add key="app" value="bs://hashed-app-id"/>
    </local>
  </capabilities>

  <environments>
    <iphone-7>
      <add key="device" value="iPhone 7"/>
    </iphone-7>
    <iphone-7-plus>
      <add key="device" value="iPhone 7 Plus"/>
    </iphone-7-plus>
  </environments>
</configuration>
```
Here is a sample test case written for running local with NUnit.
```
namespace ios
{
    [TestFixture("local", "iphone-7")]
    public class LocalTest : BrowserStackNUnitTest
    {
        public LocalTest(string profile, string environment) : base(profile,environment){ }

        [Test]
        public void testLocal()
        {
            IOSElement testButton = (IOSElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("TestBrowserStackLocal")));
            testButton.Click();

            WebDriverWait wait = new WebDriverWait(driver, TimeSpan.FromSeconds(30));
            wait.Until(ExpectedConditions.TextToBePresentInElementValue(driver.FindElement(MobileBy.AccessibilityId("ResultBrowserStackLocal")), "Response is: Up and running"));
            IOSElement resultElement = (IOSElement)driver.FindElement(MobileBy.AccessibilityId("ResultBrowserStackLocal"));

            String resultString = resultElement.Text.ToLower();
            if(resultString.Contains("not working"))
            {
                Screenshot ss = ((ITakesScreenshot)driver).GetScreenshot();
                string screenshot = ss.AsBase64EncodedString;
                byte[] screenshotAsByteArray = ss.AsByteArray;
                ss.SaveAsFile("screenshot", ScreenshotImageFormat.Png);
                ss.ToString();
            }

            String expectedString = "Up and running";

            Assert.True(resultString.Contains(expectedString.ToLower()));
        }
    }
}
```

3. You can now run your NUnit test using BrowserStack Local with the following command:
```
Build the solution in Visual Studio
Run test with fixture "local" from Test Explorer

```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple NUnit tests at the same time across various device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with NUnit on BrowserStack, modify the config file as shown below:

<Android / IOS Selector>
###### Android (Toggle)

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="single" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="parallel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
    <sectionGroup name="environments">
      <section name="galaxy-s6" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="galaxy-s7" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />  
      <section name="pixel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>

  </configSections>

  <appSettings>
    <add key="user" value="BROWSERSTACK_USERNAME" />
    <add key="key" value="BROWSERSTACK_ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <parallel>
      <add key="build" value="nunit-browserstack" />
      <add key="name" value="parallel_test" />
      <add key="browserstack.debug" value="true" />
      <add key="app" value="bs://hashed-app-id"/>
    </parallel>
  </capabilities>

  <environments>
        <galaxy-s6>
            <add key="device" value="Samsung Galaxy S6"/>
        </galaxy-s6>
        <galaxy-s7>
            <add key="device" value="Samsung Galaxy S7"/>
        </galaxy-s7>
        <pixel>
            <add key="device" value="Google Pixel"/>
        </pixel>
    </environments>
</configuration>
```
```
namespace android
{
  [TestFixture("parallel", "pixel")]
  [TestFixture("parallel", "galaxy-s6")]
  [TestFixture("parallel", "galaxy-s7")]
  [Parallelizable(ParallelScope.Fixtures)]
  public class ParallelTest : BrowserStackNUnitTest
  {
    public ParallelTest(string profile, string device) : base(profile,device){ }

    [Test]
    public void searchWikipedia()
    {
      AndroidElement searchElement = (AndroidElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("Search Wikipedia")));
      searchElement.Click();
      AndroidElement insertTextElement = (AndroidElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(By.Id("org.wikipedia.alpha:id/search_src_text")));
      insertTextElement.SendKeys("Browserstack");
      Thread.Sleep(5000);

      ReadOnlyCollection<AndroidElement> allProductsName = driver.FindElements(By.ClassName("android.widget.TextView"));
      Assert.True(allProductsName.Count > 0);
    }
  }
}
```

###### iOS (Toggle)

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <sectionGroup name="capabilities">
      <section name="single" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="local" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="parallel" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
    <sectionGroup name="environments">
      <section name="iphone-7" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
      <section name="iphone-7-plus" type="System.Configuration.AppSettingsSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" />
    </sectionGroup>
  </configSections>

  <appSettings>
    <add key="user" value="BROWSERSTACK_USERNAME" />
    <add key="key" value="BROWSERSTACK_ACCESS_KEY" />
    <add key="server" value="hub-cloud.browserstack.com" />
  </appSettings>

  <capabilities>
    <parallel>
      <add key="build" value="nunit-browserstack" />
      <add key="name" value="parallel_test" />
      <add key="browserstack.debug" value="true" />
      <add key="app" value="bs://hashed-app-id"/>
    </parallel>
  </capabilities>

  <environments>
    <iphone-7>
      <add key="device" value="iPhone 7"/>
    </iphone-7>
    <iphone-7-plus>
      <add key="device" value="iPhone 7 Plus"/>
    </iphone-7-plus>
  </environments>
</configuration>
```
```
namespace ios
{
    [TestFixture("parallel", "iphone-7")]
    [TestFixture("parallel", "iphone-7-plus")]
    [Parallelizable(ParallelScope.Fixtures)]
    public class ParallelTest : BrowserStackNUnitTest
    {
        public ParallelTest(string profile, string device) : base(profile, device) {}

        [Test]
        public void loginTest()
        {
            IOSElement loginButton = (IOSElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("Log In")));
            loginButton.Click();

            IOSElement emailTextField = (IOSElement)new WebDriverWait(driver, TimeSpan.FromSeconds(30)).Until(ExpectedConditions.ElementToBeClickable(MobileBy.AccessibilityId("Email address")));
            emailTextField.SendKeys("hello@browserstack.com");

            driver.FindElementByAccessibilityId("Next").Click();
            Thread.Sleep(5000);

            ReadOnlyCollection<IOSElement> textElements = driver.FindElementsByXPath("//XCUIElementTypeStaticText");
            Assert.True(textElements.Count > 0);
            String matchedString = "";
            foreach (IOSElement textElement in textElements)
            {
                try {
                  String textContent = textElement.Text;
                  if (textContent.Contains("not registered"))
                  {
                      matchedString = textContent;
                  }
                }
                catch (NullReferenceException) {
                  continue;
                }
            }
        }
    }
}
```

You can now run your tests in parallel on BrowserStack using the following command:
```
Build the solution in Visual Studio
Run tests with fixture "parallel" from Test Explorer
```
>Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


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
The two potential values for status can either be passed or failed. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/app-automate/appium/api-reference).

### Step 4: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Appium command run through your NUnit script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
<capabilities>
  <single>
    <add key="browserstack.debug" value="true" />
  </single>
</capabilities>
```
**Video Recording**
Every test run on the BrowserStack Appium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
<capabilities>
  <single>
    <add key="browserstack.video" value="false" />
  </single>
</capabilities>
```
In addition to these logs BrowserStack also provides Raw logs, Network logs and Appium logs. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/app-automate/appium/c-sharp-nunit/debug-failed-tests)

### Next steps
Congrats! You are now able to run your tests on BrowserStack cloud. You may want to check out these resources:
***
#### [Testing on internal networks](https://www.browserstack.com/docs/app-automate/appium/<language>/getting-started-with-local-testing)
***
#### [Run tests in parallel](https://www.browserstack.com/docs/app-automate/appium/<language>/getting-started-with-parallel-testing)
***
#### [Debug your app](https://www.browserstack.com/docs/app-automate/appium/<language>/view-test-results)
***
#### [Integrate with CICD Tools](https://www.browserstack.com/docs/app-automate/appium/<language>/jenkins)
***
### Questions?
We're always happy to help with any questions you might have! Read [FAQ](https://www.browserstack.com/support?tag=app-automate), [Contact support](https://www.browserstack.com/contact), or [connect with our sales team](https://www.browserstack.com/contact#sales).

```
Was this page helpful? Yes No
```
