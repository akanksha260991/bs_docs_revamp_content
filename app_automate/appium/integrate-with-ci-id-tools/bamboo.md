# Bamboo CI

Integrate your Appium test suite with Bamboo and BrowserStack using our plugin.

### Introduction
Bamboo is a continuous integration server that allows you to build, test and deploy your web and mobile app applications. Our Bamboo plugin enables the integration between your Appium tests, Bamboo and the BrowserStack real device cloud.

Use the BrowserStack Bamboo plugin to:
* Configure your BrowserStack credentials for your Bamboo jobs.
* Create a build step to upload your app build to the BrowserStack server.
* Set up and tear down the BrowserStack Local binary for testing mobile apps against the internal, private or development servers.
* Embed BrowserStack test results, including video, logs, and screenshots in your Bamboo job results.

### Prerequisites
Before installing the plugin please make sure that you have the necessary privileges to administer your Bamboo installation. In addition, ensure that there are no active build jobs running on Bamboo when installing the plugin. You will also need the following:
* An existing Bamboo CI server v5.13.2 or above
* A BrowserStack account


### Installing the Bamboo Plugin
Before installing the plugin please make sure that you have the necessary privileges to administer your Bamboo installation. We recommend that you do this when there are no active build jobs running on Bamboo.

1.Log into your Bamboo instance as an admin.
2. Click the admin dropdown (Settings icon on the top menu) and choose Add-ons.
3. The Manage add-ons screen will load.
4. Click Find new add-ons, located under the the ADD-ONS section on the left-hand side of the page.
5. Locate BrowserStack Plugin for Bamboo via search.
6. Results include add-on versions compatible with your Bamboo instance.
7. Click Install to download and install your add-on.
8. You're all set!


### Configuring your BrowserStack credentials
Once you've installed the plugin, you will need to configure your BrowserStack credentials to complete the integration:

1. Go to **Settings** > **Overview** > **BrowserStack Configuration** in the Bamboo administration control panel.
2. Add your BrowserStack Username and Access Key
3. Save your changes

![Using Jenkins UI](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/bamboo/bamboo_global_config_page@2x.png)

## Configuring BrowserStack Local
BrowserStack Local Testing allows you to test your apps against the private, development, and internal servers. With the Local testing binary, you can create a secure, private connection between the BrowserStack Appium and your internal servers. The BrowserStack Bamboo plugin is responsible for:

1. Downloading the BrowserStack local binary for every platform that the build job is running on.
2. Setting up and tearing down the secure tunnel.
3. The BrowserStack Bamboo plugin sets the following environment variables:

```bash
BROWSERSTACK_USERNAME
BROWSERSTACK_ACCESS_KEY
BROWSERSTACK_LOCAL
BROWSERSTACK_LOCAL_IDENTIFIER
BROWSERSTACK_APP_ID
```

Use these environment variables to set the DesiredCapabilities in your tests. For example:
```java
String username = System.getenv("BROWSERSTACK_USERNAME");
String accessKey = System.getenv("BROWSERSTACK_ACCESS_KEY");
String app = System.getenv("BROWSERSTACK_APP_ID");
String browserstackLocal = System.getenv("BROWSERSTACK_LOCAL");
String browserstackLocalIdentifier = System.getenv("BROWSERSTACK_LOCAL_IDENTIFIER");

DesiredCapabilities capabilities = new DesiredCapabilities();
capabilities.setCapability("device", "Samsung Galaxy S8");
capabilities.setCapability("app", app);
capabilities.setCapability("browserstack.local", browserstackLocal);
capabilities.setCapability("browserstack.localIdentifier", browserstackLocalIdentifier);
driver = new RemoteWebDriver(new URL("https://" + username + ":" + accessKey + "@hub.browserstack.com/wd/hub"), capabilities);
```

> Note: If you are using BrowserStack Local, you must pass browserstack.local and browserstack.localIdentifier capabilities to test on your local development servers.

To enable BrowserStack Local follow these steps:

1. Check **Enable BrowserStack Local** in the BrowserStack Configuration section. Note that in case you're already using BrowserStack Local through our bindings, you don't need to check this.
2. Use **Modifiers** to set any additional configuration (Full list of options [here](https://www.browserstack.com/local-testing#modifiers) )

![Using Jenkins UI](https://d98b8t1nnulk5.cloudfront.net/production/images/static/docs/bamboo/bamboo_global_config_page_with_local_enabled@2x.png)

## Configure app upload step for individual job
You can use BrowserStack Plugin for Bamboo to upload your app builds to the BrowserStack servers for individual jobs. You can also configure your BrowserStack credentials and local settings at the job level. Any configuration passed at job level will override the Global configuration set in the Administration section. To set a job configuration:

1. Check **Override Admin Config** inside the **Miscellaneous** tab for the job

2. You will now be able to enter your BrowserStack credentials

3. Check Enable BrowserStack Local to test on your local development servers

4. Check **Enable App Automate** and provide the path to your app file in the App Path text box

![Using Jenkins UI](https://d98b8t1nnulk5.cloudfront.net/production/images/static/docs/bamboo/config_enabled_with_overriden_admin_config@2x.png)

You can now run your App Automate tests using Bamboo plugin on BrowserStack cloud.

## Generating and Embedding test reports
BrowserStack allows you to easily debug your Appium tests with features like Video recordings, Appium logs, and Device logs. With our Bamboo plugin, you can view all the logs and reports generated by BrowserStack right inside Bamboo. At this point, embedding test reports only works with a select set of frameworks. The prerequisites are:

1. Project must be a Java project
2. Project must be built with Maven and use either TestNG or JUnit for testing


Add the following dependencies to your pom.xml file:
```xml
<dependency>
    <groupId>com.browserstack</groupId>
    <artifactId>automate-testassist</artifactId>
    <version>1.0.0-SNAPSHOT</version>
</dependency>
```


Add the following repositories to your pom.xml file:
```xml
<repositories>
  <repository>
    <id>sonatype-nexus-snapshots</id>
    <url>https://oss.sonatype.org/content/repositories/snapshots</url>
  </repository>
</repositories>
```

```xml
<pluginRepositories>
  <pluginRepository>
   <id>sonatype-nexus-snapshots</id>
      <url>https://oss.sonatype.org/content/repositories/snapshots</url>
  </pluginRepository>
</pluginRepositories>
```


Add the following plugin to your pom.xml file:
```xml
<build>
    <plugins>
      <plugin>
        <groupId>com.browserstack</groupId>
        <artifactId>automate-maven-plugin</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <configuration>
            <source>${jdk.source.version}</source>
            <target>${jdk.target.version}</target>
            <complianceLevel>${jdk.source.version}</complianceLevel>
        </configuration>
        <executions>
          <execution>
            <goals>
              <goal>test-compile</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
</build>
```
You must define the Maven properties, **jdk.source.version** and **jdk.target.version** to match the Java versions of your project.

Create a new artifact named **BSTACK_REPORTS** which stores all of the **/*.xml files generated by your test suite.


![Using Jenkins UI](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/bamboo/config_page_with_reporting@2x.png)

You should now be able to see BrowserStack test reports in the BrowserStack Report tab.

## Conclusion
By following the steps outlined in this guide, you should have a seamless integration between your existing automation in Bamboo, your Appium tests and the BrowserStack Real Device cloud. This will help you leverage all the benefits of test automation and the scale and coverage offered by BrowserStack.
