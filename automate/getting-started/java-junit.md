# Selenium with JUnit

#### Your guide to running Selenium Webdriver tests with JUnit on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with JUnit on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [junnit-browserstack](https://github.com/browserstack/junit-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with JUnit, ensure you have the JUnit libraries installed. Ensure it is included in the pom.xml, as shown below:

```
<!-- Install using maven -->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in JUnit with Java
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample JUnit test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
public class SingleTest extends BrowserStackJUnitTest {

    @Test
    public void test() throws Exception {
        driver.get("https://www.google.com/ncr");
        WebElement element = driver.findElement(By.name("q"));
        element.sendKeys("BrowserStack");
        element.submit();
        Thread.sleep(5000);

        assertEquals("BrowserStack - Google Search", driver.getTitle());
    }
}
```
Once we have defined the test case, we are ready to integrate this JUnit test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

We can now integrate our JUnit test case into BrowserStack. To start, we define a class which contains the methods to configure and create the connection with BrowserStack.

```
@RunWith(Parallelized.class)
public class BrowserStackJUnitTest {
    public WebDriver driver;
    private Local l;

    private static JSONObject config;

    @Parameter(value = 0)
    public int taskID;

    @Parameters
    public static Iterable<? extends Object> data() throws Exception {
        JSONParser parser = new JSONParser();
        config = (JSONObject) parser.parse(new FileReader("src/test/resources/conf/" + System.getProperty("config")));
        int envs = ((JSONArray)config.get("environments")).size();

        List<Integer> taskIDs = new ArrayList<Integer>();
        for(int i=0; i<envs; i++) {
            taskIDs.add(i);
        }

        return taskIDs;
    }

    @Before
    public void setUp() throws Exception {
        JSONArray envs = (JSONArray) config.get("environments");

        DesiredCapabilities capabilities = new DesiredCapabilities();

        Map<String, String> envCapabilities = (Map<String, String>) envs.get(taskID);
        Iterator it = envCapabilities.entrySet().iterator();
        while (it.hasNext()) {
            Map.Entry pair = (Map.Entry)it.next();
            capabilities.setCapability(pair.getKey().toString(), pair.getValue().toString());
        }

        Map<String, String> commonCapabilities = (Map<String, String>) config.get("capabilities");
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
            Map<String, String> options = new HashMap<String, String>();
            options.put("key", accessKey);
            l.start(options);
        }

        driver = new RemoteWebDriver(new URL("http://"+username+":"+accessKey+"@"+config.get("server")+"/wd/hub"), capabilities);
    }

    @After
    public void tearDown() throws Exception {
        driver.quit();
        if(l != null) l.stop();
    }
}
```
We now need to create a config file, single.conf.json, which contains the BrowserStack Hub URL and credentials required to connect to the BrowserStack Selenium grid.

```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "browserstack.debug": true
  },

  "environments": [{
    "browser": "chrome"
  }]
}
```
We are now ready to run the test on BrowserStack, using the following command:

```
# Run using maven
mvn test -P single
```

### Step 2: Test on internal networks

BrowserStack enables you to run your JUnit automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

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

```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "browserstack.local": true
  },

  "environments": [{
    "browser": "chrome"
  }]
}
```
Here is a sample test case written for running local with JUnit.


```
public class LocalTest extends BrowserStackJUnitTest {

    @Test
    public void test() throws Exception {
        driver.get("http://bs-local.com:45691/check");

        assertTrue(driver.getPageSource().contains("Up and running"));
    }
}
```
3. You can now run your JUnit test using BrowserStack Local with the following command:

```
# Run using maven
mvn test -P local
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple JUnit tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with JUnit on BrowserStack, modify the parallel.conf.js config file as shown below:
```
{
  "server": "hub-cloud.browserstack.com",
  "user": "USERNAME",
  "key": "ACCESS_KEY",

  "capabilities": {
    "browserstack.debug": true
  },

  "environments": [{
    "browser": "chrome"
  },{
    "browser": "firefox"
  },{
    "browser": "safari"
  },{
    "browser": "internet explorer"
  }]
}
```
Capabilities for each environment can be customised as explained earlier.
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
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your JUnit test cases.

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
A full reference of our REST API can be found [here](https://www.browserstack.com/docs/selenium/api-reference).


### Step 5: Debug your app
BrowserStack provides a range of debugging tools to help you quickly identify and fix bugs you discover through your automated tests.

**Text Logs**
Text Logs are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

**Visual Logs**
Visual Logs automatically capture the screenshots generated at every Selenium command run through your JUnit tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
"capabilities": {
  "browserstack.debug": true
}
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
"capabilities": {
  "browserstack.video": false
}
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/java-junit/debug-failed-tests)

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
