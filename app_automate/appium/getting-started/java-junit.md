# Appium with JUnit

#### Your guide to running mobile app tests using Appium with JUnit on BrowserStack real device cloud.
***
BrowserStack gives you instant access to 2000+ real devices. Running your Appium test automation with JUnit for native and hybrid mobile apps on BrowserStack is simple. This guide will help you:

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
Ensure you have JUnit libraries installed.
```
<!-- Install using maven -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
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

Different ways you can upload your app can be found [here](/docs/app-automate/appium/java-junit/upload-app).

#### 3. Configure and run test
Copy sample code provided in JUnit for Android and iOS. Update the desired capability "app" with the App URL returned from the above API call.

<Android/iOS selector>
##### Android (Toggle)

If you are using our Sample App, the sample test below will install the Sample App (Wikipedia App) on the device, search for 'browserstack' and asserts for the list of results. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for JUnit on Github: [junit-appium-app-browserstack](https://github.com/browserstack/junit-appium-app-browserstack)

```
public class SingleTest extends BrowserStackJUnitTest {
  @Test
  public void test() throws Exception {
    AndroidElement searchElement = (AndroidElement) new WebDriverWait(driver, 30).until(
        ExpectedConditions.elementToBeClickable(MobileBy.AccessibilityId("Search Wikipedia")));
    searchElement.click();
    AndroidElement insertTextElement = (AndroidElement) new WebDriverWait(driver, 30).until(
        ExpectedConditions.elementToBeClickable(MobileBy.id("org.wikipedia.alpha:id/search_src_text")));
    insertTextElement.sendKeys("BrowserStack");
    Thread.sleep(5000);

    List<AndroidElement> allProductsName = driver.findElementsByClassName("android.widget.TextView");
    assertTrue(allProductsName.size() > 0);
  }
}
```
> Note:
> 1. UIAutomator2 is the default automation engine for Android.
> 2. Pass automationName as 'Appium' if you are using Appium automation engine
> 3. If your app uses webview, set the webview debugging flag to true when creating the webview object as described in the [Android remote debugging docs](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews): WebView.setWebContentsDebuggingEnabled(true);

##### iOS (Toggle)
If you are using our iOS Sample App, the sample test below will install the Sample App (WordPress App) on the device, navigate to the Login screen, enters the login email and check whether the email is registered on WordPress. If you are using your own app, modify the code as per your test cases.

>Note: Refer our sample repo for JUnit on Github: [junit-appium-app-browserstack](https://github.com/browserstack/junit-appium-app-browserstack)

```
public class SingleTest extends BrowserStackJUnitTest {
  @Test
  public void test() throws Exception {
    IOSElement textButton = (IOSElement) new WebDriverWait(driver, 30).until(
        ExpectedConditions.elementToBeClickable(MobileBy.AccessibilityId("Text Button")));
    textButton.click();
    IOSElement textInput = (IOSElement) new WebDriverWait(driver, 30).until(
        ExpectedConditions.elementToBeClickable(MobileBy.AccessibilityId("Text Input")));
    textInput.sendKeys("hello@browserstack.com"+"\n");

    Thread.sleep(5000);

    IOSElement textOutput = (IOSElement) new WebDriverWait(driver, 30).until(
        ExpectedConditions.elementToBeClickable(MobileBy.AccessibilityId("Text Output")));

    assertEquals(textOutput.getText(),"hello@browserstack.com");
  }
}
```

>Warning: The driver.quit statement is required, otherwise the test continues to execute, leading to a timeout.

To actually run the test case, we need to integrate with BrowserStack as follows.

##### Integration with BrowserStack
Integration of JUnit with BrowserStack is made possible by use of following module:

```
@RunWith(Parallelized.class)
public class BrowserStackJUnitTest {
    public WebDriver driver;
    private Local l;

    private static JSONObject config;

    @Parameter(value = 0)
    public int taskID;

    @Parameters
    public static Iterable data() throws Exception {
        List taskIDs = new ArrayList();

        if(System.getProperty("config") != null) {
            JSONParser parser = new JSONParser();
            config = (JSONObject) parser.parse(new FileReader("src/test/resources/conf/" + System.getProperty("config")));
            int envs = ((JSONArray)config.get("environments")).size();

            for(int i=0; i<envs; i++)="" {=""  ="" taskids.add(i);="" }="" return="" taskids;="" @before="" public="" void="" setup()="" throws="" exception="" jsonarray="" envs="(JSONArray)" config.get("environments");="" desiredcapabilities="" capabilities="new" desiredcapabilities();="" map<string,="" string=""> envCapabilities = (Map<string, string="">) envs.get(taskID);
        Iterator it = envCapabilities.entrySet().iterator();
        while (it.hasNext()) {
            Map.Entry pair = (Map.Entry)it.next();
            capabilities.setCapability(pair.getKey().toString(), pair.getValue().toString());
        }

        Map<string, string=""> commonCapabilities = (Map<string, string="">) config.get("capabilities");
        it = commonCapabilities.entrySet().iterator();
        while (it.hasNext()) {
            Map.Entry pair = (Map.Entry)it.next();
            if(capabilities.getCapability(pair.getKey().toString()) == null){
                capabilities.setCapability(pair.getKey().toString(), pair.getValue().toString());
            }
        }

        String username = System.getenv("BROWSERSTACK_USERNAME");
        if(username == null) {
            username = (String) config.get("user");
        }

        String accessKey = System.getenv("BROWSERSTACK_ACCESS_KEY");
        if(accessKey == null) {
            accessKey = (String) config.get("key");
        }

        String app = System.getenv("BROWSERSTACK_APP_ID");
        if(app != null && !app.isEmpty()) {
            capabilities.setCapability("app", app);
        }

        if(capabilities.getCapability("browserstack.local") != null && capabilities.getCapability("browserstack.local") == "true"){
            l = new Local();
            Map<string, string=""> options = new HashMap<string, string="">();
            options.put("key", accessKey);
            l.start(options);
        }
        driver = new AndroidDriver(new URL("http://"+username+":"+accessKey+"@"+config.get("server")+"/wd/hub"), capabilities);
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
        if(l != null) l.stop();
    }
}
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials. If the BROWSERSTACK_USERNAME and BROWSERSTACK_ACCESS_KEY are set in Environment variables, then that will take a preference. Also, update the App URL after uploading App to the BrowserStack cloud.

<Android / IOS Selector>
###### Android (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "junit-browserstack",
    "name": "single_appium_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "Samsung Galaxy S6"
  }]
}
```
###### iOS (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "junit-browserstack",
    "name": "single_appium_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "iPhone 7 Plus"
  }]
}
```
Run your test on BrowserStack using following command:

```
# Run using maven
mvn test -P single
```

### Step 2: Test on internal networks

BrowserStack enables you to run automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

>Note:  In order to try a local test, upload our Local Sample App for [Android](https://www.browserstack.com/app-automate/sample-apps/android/LocalSample.apk) and [iOS](https://www.browserstack.com/app-automate/sample-apps/ios/LocalSample.ipa)

Configuring your JUnit tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:

```
<!-- Install using maven -->
<dependency>
    <groupId>com.browserstack</groupId>
    <artifactId>browserstack-local-java</artifactId>
    <version>0.1.0</version>
</dependency>
```
2. Next, you need to update your JUnit config file, local.conf.json, and set the browserstack.local capability to true:

<Android / IOS Selector>
###### Android (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "junit-browserstack",
    "name": "local_appium_test",
    "browserstack.debug": true,
    "browserstack.local": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "Samsung Galaxy S6"
  }]
}
```

Here is a sample test case written for running local with JUnit.

```
public class LocalTest extends BrowserStackJUnitTest {

  @Test
  public void test() throws Exception {
    AndroidElement searchElement = (AndroidElement) new WebDriverWait(driver, 30).until(
        ExpectedConditions.elementToBeClickable(MobileBy.id("com.example.android.basicnetworking:id/test_action")));
    searchElement.click();
    AndroidElement insertTextElement = (AndroidElement) new WebDriverWait(driver, 30).until(
        ExpectedConditions.elementToBeClickable(MobileBy.className("android.widget.TextView")));

    AndroidElement testElement = null;
    List<AndroidElement> allTextViewElements = driver.findElementsByClassName("android.widget.TextView");
    Thread.sleep(10);
    for(AndroidElement textElement : allTextViewElements) {
      if(textElement.getText().contains("The active connection is")) {
        testElement = textElement;
      }
    }

    if(testElement == null) {
      File scrFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
      FileUtils.copyFile(scrFile, new File(System.getProperty("user.dir") + "screenshot.png"));
      System.out.println("Screenshot stored at " + System.getProperty("user.dir") + "screenshot.png");
      throw new Error("Cannot find the needed TextView element from app");
    }
    String matchedString = testElement.getText();
    System.out.println(matchedString);
    assertTrue(matchedString.contains("The active connection is wifi"));
    assertTrue(matchedString.contains("Up and running"));
  }
}
```
###### iOS (Toggle)
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "junit-browserstack",
    "name": "local_appium_test",
    "browserstack.debug": true,
    "browserstack.local": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "iPhone 7"
  }]
}
```
Here is a sample test case written for running local with JUnit.
```
public class LocalTest extends BrowserStackJUnitTest {
  @Test
  public void test() throws Exception {
    IOSElement testButton = (IOSElement) new WebDriverWait(driver, 30).until(
      ExpectedConditions.elementToBeClickable(MobileBy.AccessibilityId("TestBrowserStackLocal")));
    testButton.click();

    WebDriverWait wait = new WebDriverWait(driver, 30);
    wait.until(new ExpectedCondition<Boolean>() {
      @Override
      public Boolean apply(WebDriver d) {
        String result = d.findElement(MobileBy.AccessibilityId("ResultBrowserStackLocal")).getAttribute("value");
        return result != null && result.length() > 0;
      }
    });
    IOSElement resultElement = (IOSElement) driver.findElement(MobileBy.AccessibilityId("ResultBrowserStackLocal"));

    String resultString = resultElement.getText().toLowerCase();
    System.out.println(resultString);
    if(resultString.contains("not working")) {
      File scrFile = (File) ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
      FileUtils.copyFile(scrFile, new File(System.getProperty("user.dir") + "/screenshot.png"));
      System.out.println("Screenshot stored at " + System.getProperty("user.dir") + "/screenshot.png");
      throw new Error("Unexpected BrowserStackLocal test result");
    }

    String expectedString = "Up and running";
    assertTrue(resultString.contains(expectedString.toLowerCase()));
  }
}
```

3. You can now run your JUnit test using BrowserStack Local with the following command:
```
# Run using maven
mvn test -P local

```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple JUnit tests at the same time across various device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with JUnit on BrowserStack, modify the parallel.conf.js config file as shown below:

<Android / IOS Selector>
###### Android (Toggle)

```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "junit-browserstack",
    "name": "parallel_appium_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "Google Pixel"
  },{
    "device": "Samsung Galaxy S7"
  },{
    "device": "Samsung Galaxy S6"
  },{
    "device": "Google Nexus 9"
  }]
}
```
###### iOS (Toggle)

```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "build": "junit-browserstack",
    "name": "parallel_appium_test",
    "browserstack.debug": true,
    "app": "bs://<hashed app-id>"
  },

  "environments": [{
    "device": "iPhone 7"
  }, {
    "device": "iPhone 7 Plus"
  }]
}
```

You need the following custom script to launch the tests in parallel.
```
public class Parallelized extends Parameterized {
  private static class ThreadPoolScheduler implements RunnerScheduler {
    private ExecutorService executor;

    public ThreadPoolScheduler() {
      String threads = System.getProperty("junit.parallel.threads", "16");
      int numThreads = Integer.parseInt(threads);
      executor = Executors.newFixedThreadPool(numThreads);
    }

    @Override
    public void finished() {
      executor.shutdown();
      try {
        executor.awaitTermination(10, TimeUnit.MINUTES);
      } catch (InterruptedException exc) {
        throw new RuntimeException(exc);
      }
    }

    @Override
    public void schedule(Runnable childStatement) {
      executor.submit(childStatement);
    }
  }

  public Parallelized(Class klass) throws Throwable {
    super(klass);
    setScheduler(new ThreadPoolScheduler());
  }
}
```
You can now run your tests in parallel on BrowserStack using the following command:
```
# Run using maven
mvn test -P parallel
```
>Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your JUnit test cases.

```
public static void mark() throws URISyntaxException, UnsupportedEncodingException, IOException {
    URI uri = new URI("https://USERNAME:ACCESS_KEY@api-cloud.browserstack.com/app-automate/sessions/<session-id>.json");
    HttpPut putRequest = new HttpPut(uri);

    ArrayList<NameValuePair> nameValuePairs = new ArrayList<NameValuePair>();
    nameValuePairs.add((new BasicNameValuePair("status", "completed")));
    nameValuePairs.add((new BasicNameValuePair("reason", "")));
    putRequest.setEntity(new UrlEncodedFormEntity(nameValuePairs));

    HttpClientBuilder.create().build().execute(putRequest);
}
```
The two potential values for status can either be passed or failed. Optionally, a reason can also be passed.
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/app-automate/appium/api-reference).

### Step 4: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Appium command run through your JUnit script. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
"capabilities": {
  "browserstack.debug": true
}
```
**Video Recording**
Every test run on the BrowserStack Appium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/app-automate/appium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
"capabilities": {
  "browserstack.video": false
}
```
In addition to these logs BrowserStack also provides Raw logs, Network logs and Appium logs. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/app-automate/appium/java-junit/debug-failed-tests)

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
