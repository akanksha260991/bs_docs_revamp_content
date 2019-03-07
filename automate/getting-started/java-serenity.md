# Selenium with Serenity

#### Your guide to running Selenium Webdriver tests with Serenity on BrowserStack.

***
BrowserStack gives you instant access to our Selenium Grid of 2000+ real devices and desktop browsers. Running your Selenium tests with Serenity on BrowserStack is simple. This guide will help you:

1. Run your first test
2. Test on internal networks
3. Run tests in parallel
4. Mark tests as pass / fail
5. Debug your app

> Note: All the code samples and files referred to in this guide can be found in our GitHub repo: [serenity-browserstack](https://github.com/browserstack/serenity-browserstack)

### Prerequisites:
Before you can start running your Selenium tests with Serenity, ensure you have following libraries installed:

```
<!-- Install using maven -->
<dependency>
    <groupId>net.serenity-bdd</groupId>
    <artifactId>serenity-core</artifactId>
    <version>1.1.10</version>
</dependency>
```

***

### Step 1: Run your first test

To understand how to integrate with BrowserStack, we will look at two things:

1. A sample test case written in Serenity with Java
2. Integration of this sample test case with BrowserStack

#### Sample Test Case
The sample Serenity test case below searches for the string "BrowserStack" on Google, and checks if the title of the resulting page is "BrowserStack - Google Search"

```
# Google Feature
Feature: Google Search Functionality

    Scenario: Can find search results
        When I type query as "BrowserStack"
        And I submit
        Then I should see title "BrowserStack - Google Search"
```
```
// Google Page
@DefaultUrl("https://www.google.com/ncr")
public class GooglePage extends PageObject {

    @FindBy(name="q")
    WebElementFacade search;

    @FindBy(name="btnG")
    WebElementFacade searchButton;

    public void searchForString(String searchString) {
        search.sendKeys(searchString, Keys.ENTER);
    }

    public void submitForm() throws Exception {
        searchButton.click();
        Thread.sleep(5000);
    }

    public void titleShouldMatch(String matchTitle) {
        assertThat(this.getTitle()).containsIgnoringCase(matchTitle);
    }
}
```
```
// Google Test
@RunWith(CucumberWithSerenity.class)
@CucumberOptions(features="src/test/resources/features/single.feature")
public class SingleTest extends BrowserStackSerenityTest { }
```

To actually run the test case, we need to integrate with BrowserStack as follows.


#### Integrating with BrowserStack

> Note: Running your Selenium tests on BrowserStack requires a username and an access key.

To obtain your username and access keys, [sign up for a Free Trial](https://www.browserstack.com/users/sign_up) or [purchase a plan](https://www.browserstack.com/pricing).

Integration of Serenity with BrowserStack is made possible by use of following module:

```
public class BrowserStackSerenityDriver implements DriverSource {

    public WebDriver newDriver() {
        EnvironmentVariables environmentVariables = SystemEnvironmentVariables.createEnvironmentVariables();

        String username = System.getenv("BROWSERSTACK_USERNAME");
        if(username == null) {
            username = (String) environmentVariables.getProperty("browserstack.user");
        }

        String accessKey = System.getenv("BROWSERSTACK_ACCESS_KEY");
        if(accessKey == null) {
            accessKey = (String) environmentVariables.getProperty("browserstack.key");
        }

        String environment = System.getProperty("environment");
        DesiredCapabilities capabilities = new DesiredCapabilities();

        Iterator it = environmentVariables.getKeys().iterator();
        while(it.hasNext()){
            String key = (String) it.next();

            if(key.equals("browserstack.user") || key.equals("browserstack.key") || key.equals("browserstack.server")){
                continue;
            }
            else if(key.startsWith("browserstack.")){
                capabilities.setCapability(key.replace("browserstack.", ""), environmentVariables.getProperty(key));
                if(key.equals("browserstack.local")){
                    System.setProperty("browserstack.local", "true");
                }
            }
            else if(environment != null && key.startsWith("environment." + environment)){
                capabilities.setCapability(key.replace("environment." + environment + ".", ""), environmentVariables.getProperty(key));
                if(key.equals("environment." + environment + ".browserstack.local")){
                    System.setProperty("browserstack.local", "true");
                }
            }
        }

        try {
            return new RemoteWebDriver(new URL("http://"+username+":"+accessKey+"@"+environmentVariables.getProperty("browserstack.server")+"/wd/hub"), capabilities);    
        }
        catch(Exception e){
            System.out.println(e);
            return null;
        }
    }

    public boolean takesScreenshots() {
        return true;
    }
}
public class BrowserStackSerenityTest {
    static Local bsLocal;

    @BeforeClass
    public static void setUp() throws Exception {
        EnvironmentVariables environmentVariables = SystemEnvironmentVariables.createEnvironmentVariables();

        String accessKey = System.getenv("BROWSERSTACK_ACCESS_KEY");
        if(accessKey == null) {
            accessKey = (String) environmentVariables.getProperty("browserstack.key");
        }

        String environment = System.getProperty("environment");
        String key = "browserstack.local";
        boolean is_local = environmentVariables.getProperty(key) != null && environmentVariables.getProperty(key).equals("true");

        if(environment != null && !is_local){
            key = "environment."+environment+".browserstack.local";
            is_local = environmentVariables.getProperty(key) != null && environmentVariables.getProperty(key).equals("true");
        }

        if(is_local){
            bsLocal = new Local();
            Map<String, String> bsLocalArgs = new HashMap<String, String>();
            bsLocalArgs.put("key", accessKey);
            bsLocal.start(bsLocalArgs);
        }
    }

    @AfterClass
    public static void tearDown() throws Exception {
        if(bsLocal != null) {
            bsLocal.stop();
        }
    }
}
```
The module reads from config file where you need to put the BrowserStack Hub URL and credentials.

```
webdriver.driver = provided
webdriver.provided.type = mydriver
webdriver.provided.mydriver = com.browserstack.BrowserStackSerenityDriver
serenity.driver.capabilities = mydriver

browserstack.user=USERNAME
browserstack.key=ACCESS_KEY
browserstack.server=hub-cloud.browserstack.com

environment.single.browser=chrome
```

Run your test on BrowserStack using following command:

```
# Run using maven
mvn test -P single
```

### Step 2: Test on internal networks

BrowserStack enables you to run your Serenity automated tests on your internal development environments, on localhost, and from behind a corporate firewall. This feature is called "Local Testing".

Local Testing establishes a secure connection between your machine and the BrowserStack cloud. Once you set up Local Testing, all URLs work out of the box, including HTTPS URLs and those behind a proxy or firewall.

Configuring your Serenity tests for Local Testing takes just three steps:

1. Download and install the BrowserStackLocal binary:
```
<!-- Install using maven -->
<dependency>
    <groupId>com.browserstack</groupId>
    <artifactId>browserstack-local-java</artifactId>
    <version>0.1.0</version>
</dependency>
```

2. Next, you need to update your Serenity config file and set the browserstack.local capability to true:

```
webdriver.driver = provided
webdriver.provided.type = mydriver
webdriver.provided.mydriver = com.browserstack.BrowserStackSerenityDriver
serenity.driver.capabilities = mydriver

browserstack.user=USERNAME
browserstack.key=ACCESS_KEY
browserstack.server=hub-cloud.browserstack.com

environment.local.browser=chrome
environment.local.browserstack.local=true
```
Here is a sample test case written for running local with Serenity.


```
// Local Feature
Feature: BrowserStack Local Testing

    Scenario: Can check tunnel working
        Then I should see "Up and running"
```
```
// Local Page
@DefaultUrl("http://bs-local.com:45691/check")
public class LocalPage extends PageObject {
    private WebDriver driverInstance;

    public LocalPage(WebDriver driver){
        super(driver);
        driverInstance = driver;
    }

    public void bodyShouldMatch(String matchTitle) {
        assertThat(driverInstance.getPageSource()).containsIgnoringCase(matchTitle);
    }  
}
```
```
// Local Test
@RunWith(CucumberWithSerenity.class)
@CucumberOptions(features="src/test/resources/features/local.feature")
public class LocalTest extends BrowserStackSerenityTest { }
```
3. You can now run your Serenity test using BrowserStack Local with the following command:

```
# Run using maven
mvn test -P local
```

### Step 3: Run tests in parallel
On BrowserStack, you can run multiple Serenity tests at the same time across various browser, device and OS combinations. This is “Parallel Testing”. Parallel Testing gives you the same benefits as running a multi-threaded application helps you reduce the run time of your test suite, resulting in faster build times and faster releases.

To run tests on multiple browsers in parallel with Serenity on BrowserStack, modify the config file as shown below:
```
webdriver.driver = provided
webdriver.provided.type = mydriver
webdriver.provided.mydriver = com.browserstack.BrowserStackSerenityDriver
serenity.driver.capabilities = mydriver

browserstack.user=USERNAME
browserstack.key=ACCESS_KEY
browserstack.server=hub-cloud.browserstack.com

environment.parallel_1.browser=chrome
environment.parallel_2.browser=firefox
environment.parallel_3.browser=safari
environment.parallel_4.browser=ie
```
Capabilities for each environment can be customised as explained earlier.

Parallel test cases need to be written for running in parallel with Serenity.
```
@RunWith(CucumberWithSerenity.class)
@CucumberOptions(features="src/test/resources/features/single.feature")
public class ParallelChromeTest extends BrowserStackSerenityTest { }
```
```
@RunWith(CucumberWithSerenity.class)
@CucumberOptions(features="src/test/resources/features/single.feature")
public class ParallelFirefoxTest extends BrowserStackSerenityTest { }
```

You can now run your tests in parallel on BrowserStack using the following command:
```
# Run using maven
mvn test -P parallel
```
> Note: Achieve your test coverage and build execution time goals by using our [calculator](https://www.browserstack.com/automate/parallel-calculator) to understand how many parallel sessions you need.


### Step 4: Mark tests as pass / fail
BrowserStack provides a comprehensive REST API to access and update information about your tests. Shown below is a sample code snippet which allows you to mark your tests as pass or fail based on the assertions in your Serenity test cases.

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
Visual Logs automatically capture the screenshots generated at every Selenium command run through your Serenity tests. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**:
```
browserstack.browserstack.debug = true
```
**Video Recording**
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails. You can access videos from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/docs/selenium/api-reference).

>Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
browserstack.browserstack.video = false
```

In addition to these logs BrowserStack also provides Raw logs, Network logs, Console logs, Selenium logs, Appium logs and Interactive session. Complete details to enable all the debugging options can be found [here](https://www.browserstack.com/docs/automate/selenium/java-serenity/debug-failed-tests)

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
