# Integrate BrowserStack Automate with Bamboo

## Introduction
Bamboo is a continuous integration server that allows you to build, test and deploy your web applications. Our Bamboo plugin enables the integration between your Selenium Webdriver tests, Bamboo and the BrowserStack Selenium grid.

Use the BrowserStack Bamboo plugin to:

* Configure your BrowserStack credentials for your Bamboo jobs.
* Set up and tear down the BrowserStack Local binary for testing websites hosted on internal, private or development servers.
* Embed BrowserStack test results, including video, logs, and screenshots in your Bamboo job results.

## Prerequisites
Before installing the plugin please make sure that you have the necessary privileges to administer your Bamboo installation. In addition, ensure that there are no active build jobs running on Bamboo when installing the plugin. You will also need the following:

* An existing Bamboo CI server v5.13.2 or above
* A BrowserStack account

## Installing the Bamboo Plugin
Before installing the plugin please make sure that you have the necessary privileges to administer your Bamboo installation. We recommend that you do this when there are no active build jobs running on Bamboo.

1. Log into your Bamboo instance as an admin.
2. Click the admin dropdown (**Settings** icon on the top menu) and choose **Add-ons**.
3. The Manage add-ons screen will load.
4. Click **Find new add-ons**, located under the the **ADD-ONS** section on the left-hand side of the page.
5. Locate **BrowserStack Plugin for Bamboo** via search.
6. Results include add-on versions compatible with your Bamboo instance.
7. Click **Install** to download and install your add-on.
8. You're all set!

## Configuring your BrowserStack credentials
Once you've installed the plugin, you will need to configure your BrowserStack credentials to complete the integration:

1. Go to **Settings** > **Overview** > **BrowserStack Configuration** in the Bamboo administration control panel.
2. Add your BrowserStack Username and Access Key.
3. Save your changes.

![Bamboo Global Configuration](https://www.browserstack.com/images/static/docs/bamboo/global_configuration.png)

You can now trigger your Selenium tests to run on BrowserStack through Bamboo.

## Configuring BrowserStack Local
[BrowserStack Local](https://www.browserstack.com/local-testing) Testing allows you to test web applications hosted on private, development, and internal servers. With the Local testing binary, you can create a secure, private connection between the BrowserStack Selenium Grid and your internal servers. The BrowserStack Bamboo plugin is responsible for:

1. Downloading the BrowserStack local binary for every platform that the build job is running on
2. Setting up and tearing down the secure tunnel.

The BrowserStack Bamboo plugin sets the following environment variables:

```bash
BROWSERSTACK_USERNAME
BROWSERSTACK_ACCESS_KEY
BROWSERSTACK_LOCAL
BROWSERSTACK_LOCAL_IDENTIFIER
```

Use these environment variables to set the `DesiredCapabilities` in your tests. For example:

```java
String username = System.getenv("BROWSERSTACK_USERNAME");
String accessKey = System.getenv("BROWSERSTACK_ACCESS_KEY");
String browserstackLocal = System.getenv("BROWSERSTACK_LOCAL");
String browserstackLocalIdentifier = System.getenv("BROWSERSTACK_LOCAL_IDENTIFIER");

DesiredCapabilities capabilities = new DesiredCapabilities();
capabilities.setCapability("os", "Windows");
capabilities.setCapability("browser", "chrome");
capabilities.setCapability("browserstack.local", browserstackLocal);
capabilities.setCapability("browserstack.localIdentifier", browserstackLocalIdentifier);
driver = new RemoteWebDriver(new URL("https://" + username + ":" + accessKey + "@hub.browserstack.com/wd/hub"), capabilities);
```

**Note:** You must pass `browserstack.local` and `browserstack.localIdentifier` capabilities to test on your local development servers.

To enable BrowserStack Local follow these steps:

* Check **Enable BrowserStack Local** in the BrowserStack Configuration section. Note that in case you're already using BrowserStack Local through our bindings, you don't need to check this.
* Use **Modifiers** to set any additional configuration (Full list of options [here](https://www.browserstack.com/local-testing#modifiers))

![Bamboo Local Configuration](https://www.browserstack.com/images/static/docs/bamboo/enabling_local.png)

Once you've integrated BrowserStack Local through the Bamboo plugin, you can run your Selenium tests on websites hosted on private, development, and internal servers safely and securely.

## Configuring settings for individual jobs
You can use BrowserStack Plugin for Bamboo to configure individual jobs. Any configuration passed at job level will override the Global configuration set in the Administration section. To set a job configuration:

1. Check **Override Admin Config** inside the **Miscellaneous** tab for the job.
2. You will now be able to enter your BrowserStack credentials.
3. Check **Enable BrowserStack Local** to test on your local development servers.

![Bamboo Job Configuration](https://www.browserstack.com/images/static/docs/bamboo/job_level_config.png)

## Generating and Embedding test reports
BrowserStack allows you to easily debug your Selenium Webdriver tests with features like Video recordings, Selenium logs, JS Console logs, and Network logs. With our Bamboo plugin, you can view all the logs and reports generated by BrowserStack right inside Bamboo. At this point, embedding test reports only works with a select set of frameworks. The prerequisites are:

1. Project must be a Java project.
2. Project must be built with Maven and use either TestNG or JUnit for testing.

Add the following dependencies to your `pom.xml` file:

```xml
<dependency>
    <groupId>com.browserstack</groupId>
    <artifactId>automate-testassist</artifactId>
    <version>1.0.0-SNAPSHOT</version>
</dependency>
```

Add the following repositories to your `pom.xml` file:

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
Add the following plugin to your `pom.xml` file:

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

You must define the Maven properties, `jdk.source.version` and `jdk.target.version` to match the Java versions of your project.

Create a new artifact named `BSTACK_REPORTS` which stores all of the `**/*.xml` files generated by your test suite.

![Bamboo Artifacts](https://www.browserstack.com/images/static/docs/bamboo/artifacts.png)

You should now be able to see BrowserStack test reports in the BrowserStack Report tab

## Conclusion
By following the steps outlined in this guide, you should have a seamless integration between your existing automation in Bamboo, your Selenium tests and the BrowserStack Selenium grid. This will help you leverage all the benefits of test automation and the scale and coverage offered by BrowserStack.
