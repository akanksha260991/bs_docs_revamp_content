# Selenium with JBehave

#### Your guide to running Selenium Webdriver tests with JBehave on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with JBehave on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [jbehave-browserstack](https://github.com/browserstack/jbehave-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with JBehave, ensure you have the JBehave libraries installed. Ensure it is included in the pom.xml, as shown below:

```
<!-- Install using maven -->
<dependency>
    <groupId>org.jbehave</groupId>
    <artifactId>jbehave-core</artifactId>
    <version>3.8</version>
</dependency>
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in JBehave with Java
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample JBehave test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
// Google Story
Scenario: Google Search Functionality

When I type query as "BrowserStack"
Then I submit
Then I should see title "BrowserStack - Google Search"
```
We also need the Page, Steps definition and the Embedder.
```
// Google Page
public class GooglePage {
    private String DEFAULT_GOOGLE_URL = "https://www.google.com/ncr";
    private WebDriver driver;
    private WebElement element;

    public GooglePage(WebDriver driver) {
       this.driver = driver;
    }

    public String getTitle(){
        return driver.getTitle();
    }

    public void searchOnGoogle(String keyword) {
        driver.get(DEFAULT_GOOGLE_URL);
        element = driver.findElement(By.name("q"));
        element.sendKeys(keyword);
    }

    public void submitSearch() throws Exception {
        element.submit();
        Thread.sleep(5000);
    }
}
```
```
// Google Steps
public class GoogleSteps {
    private GooglePage page;

    public GoogleSteps(WebDriver driver) {
       page = new GooglePage(driver);
    }

    @When("I type query as \"$keyword\"")
    public void searchGoogle(String keyword) {
        page.searchOnGoogle(keyword);
    }

    @Then("I submit")
    public void submit() throws Exception {
        page.submitSearch();
    }

    @Then("I should see title \"$keyword\"")
    public void titleShouldContain(String keyword) {
        Assert.assertEquals(keyword, page.getTitle());
    }
}
```
```
// Embedder
public class SingleEmbedder extends Embedder {
    private WebDriver driver;

    public SingleEmbedder(WebDriver driver) {
        this.driver = driver;
    }

    @Override
    public EmbedderControls embedderControls() {
        return new EmbedderControls().doIgnoreFailureInStories(true).doIgnoreFailureInView(true);
    }

    @Override
    public Configuration configuration() {
        Class<? extends SingleEmbedder> embedderClass = this.getClass();
        return new MostUsefulConfiguration()
            .useStoryLoader(new LoadFromClasspath(embedderClass.getClassLoader()))
            .useStoryReporterBuilder(new StoryReporterBuilder()
                .withCodeLocation(CodeLocations.codeLocationFromClass(embedderClass))
                .withDefaultFormats()
                .withCrossReference(new CrossReference()))
            .useParameterConverters(new ParameterConverters()
                .addConverters(new DateConverter(new SimpleDateFormat("yyyy-MM-dd"))))
            .useParameterControls(new ParameterControls().useNameDelimiterLeft("<").useNameDelimiterRight(">"))
            .useStepPatternParser(new RegexPrefixCapturingPatternParser(
                  "$"))
            .useStepMonitor(new SilentStepMonitor());
    }

    @Override
    public InjectableStepsFactory stepsFactory() {
        return new InstanceStepsFactory(configuration(), new GoogleSteps(driver));
    }

}
```


Once we have defined the test case, we are ready to integrate this JBehave test case into BrowserStack.

#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

We can now integrate our JBehave test case into BrowserStack. To start, we define a class which contains the methods to configure and create the connection with BrowserStack.

```
@RunWith(Parameterized.class)
public class BrowserStackJBehaveRunner {

    public WebDriver driver;
    private Local l;

    private static JSONObject config;

    @Parameter(value = 0)
    public int taskID;

    @Parameters
    public static Collection<Object[]> data() throws Exception {
        JSONParser parser = new JSONParser();
        config = (JSONObject) parser.parse(new FileReader("src/test/resources/conf/" + System.getProperty("config")));
        int envs = ((JSONArray)config.get("environments")).size();

        List<Object[]> taskIDs = new ArrayList<Object[]>();
        for(int i=0; i<envs; i++) {
            taskIDs.add(new Object[] {i});
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

    @Test
    public void runStories() throws Exception {
        Class<?> c = Class.forName(System.getProperty("embedder"));
        Constructor<?> cons = c.getConstructor(WebDriver.class);
        Embedder storyEmbedder = (Embedder) cons.newInstance(driver);

        List<String> storyPaths = Arrays.asList(System.getProperty("stories"));
        storyEmbedder.runStoriesAsPaths(storyPaths);
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

BrowserStack enables you to run your JBehave automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your JBehave tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:
```
<!-- Install using maven -->
<dependency>
    <groupId>com.browserstack</groupId>
    <artifactId>browserstack-local-java</artifactId>
    <version>0.1.0</version>
</dependency>
```

2. Next, you need to update your JBehave config file, local.conf.json, and set the browserstack.local capability to true:

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
Here is a sample test case written for running local with JBehave.

```
// Local Story
Scenario: Can check tunnel working

When I open health check
Then I should see "Up and running"
```
We also need to define the Page, Steps and Embedder.

```
// Local Page
public class LocalPage {
    private String DEFAULT_LOCAL_URL = "http://bs-local.com:45691/check";
    private WebDriver driver;
    private WebElement element;

    public LocalPage(WebDriver driver) {
        this.driver = driver;
    }

    public String getPageSource(){
        return driver.getPageSource();
    }

    public void openHealthCheck() {
       driver.get(DEFAULT_LOCAL_URL);
    }
}
```
```
// Local Steps
public class LocalSteps {
    private LocalPage page;

    public LocalSteps(WebDriver driver) {
        page = new LocalPage(driver);
    }

    @When("I open health check")
    public void openHealthCheck() {
        page.openHealthCheck();
    }

    @Then("I should see \"$keyword\"")
    public void pageShouldContain(String keyword) {
        Assert.assertTrue(page.getPageSource().contains(keyword));
    }
}
```
```
// Embedder
public class LocalEmbedder extends Embedder {
    private WebDriver driver;

    public LocalEmbedder(WebDriver driver) {
        this.driver = driver;
    }

    @Override
    public EmbedderControls embedderControls() {
        return new EmbedderControls().doIgnoreFailureInStories(true).doIgnoreFailureInView(true);
    }

    @Override
    public Configuration configuration() {
        Class<? extends LocalEmbedder> embedderClass = this.getClass();
        return new MostUsefulConfiguration()
            .useStoryLoader(new LoadFromClasspath(embedderClass.getClassLoader()))
            .useStoryReporterBuilder(new StoryReporterBuilder()
                .withCodeLocation(CodeLocations.codeLocationFromClass(embedderClass))
                .withDefaultFormats()
                .withCrossReference(new CrossReference()))
            .useParameterConverters(new ParameterConverters()
                .addConverters(new DateConverter(new SimpleDateFormat("yyyy-MM-dd"))))
            .useParameterControls(new ParameterControls().useNameDelimiterLeft("<").useNameDelimiterRight(">"))
            .useStepPatternParser(new RegexPrefixCapturingPatternParser(
                  "$"))
            .useStepMonitor(new SilentStepMonitor());
    }

    @Override
    public InjectableStepsFactory stepsFactory() {
        return new InstanceStepsFactory(configuration(), new LocalSteps(driver));
    }

}
```
3. You can now run your JBehave test using BrowserStack Local with the following command:

```
# Run using maven
mvn test -P local
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple JBehave tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with JBehave on BrowserStack, modify the parallel.conf.js config file as shown below:
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

You can now run your tests in parallel on BrowserStack using the following command:
```
# Run using maven
mvn test -P parallel
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your JBehave test cases.

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
Visual Logs automatically capture the screenshots generated at every Selenium command run through your JBehave tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

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

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/java-jbehave/debug-failed-tests)

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
