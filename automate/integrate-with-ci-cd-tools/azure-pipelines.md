# Integrate BrowserStack Automate with Azure Pipelines

## Introduction
Azure Pipelines formerly known as Visual Studio Team Services(VSTS) is a Microsoft product. Azure Pipelines is a Continuous Integration (CI) server, which automates the build and deploy process of your web applications. By running your Selenium test suite in Azure Pipelines, you also automate testing as part of the build process. Add BrowserStack to the mix, and your web apps can be tested across 2000+ real devices and desktop browsers. BrowserStack lets you integrate your Selenium tests with Azure Pipelines using an extension that connects the CI server to the BrowserStack Selenium Grid.

Use the BrowserStack Azure Pipelines extension to:

* Configure your BrowserStack credentials.
* Set up and tear down the BrowserStack Local binary for testing internal, development and staging environments.
* Embed BrowserStack test results in your job results.

## Prerequisites
You need the following set up before you begin.

* Azure Pipelines account
* BrowserStack account

## Installing Azure Pipelines Extension
To run your tests with Azure Pipelines on BrowserStack, you will first need to install our extension. Before installing the plugin, ensure you have the necessary privileges to administer the installation.

1. Go to the BrowserStack extension [page](https://marketplace.visualstudio.com/items?itemName=browserstackcom.browserstack-vsts-extension).
2. Click on Get it free. It will redirect to the installation page.

![Visual Studio MarketPlace Installation Page](https://www.browserstack.com/images/static/docs/azure/installation-page.png)


3. Use the drop-down Select an Azure Pipelines organization to select your organization. Click Install.

![Organization Selector Drop down](https://www.browserstack.com/images/static/docs/azure/installation-page-dropdown.png)

This completes the extension installation.

## Configuring Build Pipeline
Once you install the extension, you can configure a build pipeline. You can edit an existing pipeline or create a new one.

Select your organization, project and click on **Pipelines** > **Builds**.

To configure build pipeline with BrowserStack follow the steps.

1. Select Agent job 1(it is a job name, it could be different if you are editing an existing job) and click on the + icon. Type browserstack in the search box.

![Build Pipeline Agent Selector](https://www.browserstack.com/images/static/docs/azure/build-pipeline-agent-selector.png)

2. Hover on **BrowserStack Config** and click on the **Add** button.
3. Select **BrowserStack configuration setup** click on the **NEW** button to set up BrowserStack credentials.

![Setup Pipeline Credentials](https://www.browserstack.com/images/static/docs/azure/build-pipeline-setup-creds.png)

4. In the popup, enter a name in **Connection name** field. Enter your BrowserStack **Username** and **AccessKey**. You can get your credentials from the [automate dashboard page](https://automate.browserstack.com/dashboard). Use the **Verify connection** button to verify the connection.

![Verify Pipeline Connection](https://www.browserstack.com/images/static/docs/azure/build-pipeline-verify-connection.png)

5. Click **OK** to save the connection.

## Configuring BrowserStack Local
[BrowserStack Local Testing](https://www.browserstack.com/local-testing) allows you to test web applications hosted on private, development, and internal servers. With the Local testing binary, you can create a secure, private connection between the BrowserStack Selenium Grid and your internal servers.

The BrowserStack Azure Pipelines extension sets the following environment variables:

```bash
BROWSERSTACK_USERNAME
BROWSERSTACK_ACCESS_KEY
BROWSERSTACK_LOCAL
BROWSERSTACK_LOCAL_IDENTIFIER
```

Use these environment variables to set the DesiredCapabilities in your tests. For example:

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

### Starting BrowserStack Local
To enable BrowserStack Local follow these steps:

1. Under BrowserStack configuration step, click on the checkmark next to **BrowserStackLocal**.

![Starting BrowserStack Local](https://www.browserstack.com/images/static/docs/azure/starting-bs-local.png)

2. Use **BrowserStack Local Options** to set any additional configuration options when running the binary.

  For example to enable verbose logging and to force local, pass

  ```java
  {"verbose": true, "forceLocal": true}
  ```

  in the BrowserStack Local Options. The full list of options available [here](https://www.browserstack.com/local-testing#modifiers).

  Please use camel case in local modifier options, for example, use logFile instead of --log-file

### Stopping BrowserStack Local
Please note that stopping BrowserStack Local is only required if you enabled Local in the above step.

To stop BrowserStack Local follow these steps:

1. Select Agent job 1 and click on the + icon. Type browserstack in the search box.
2. Hover on **BrowserStack Stop Local** and click on the **Add** button.

![Stopping BrowserStack Local](https://www.browserstack.com/images/static/docs/azure/stopping-bs-local.png)

That's it. This task will stop the BrowserStack Local.

Please note that the step to stop Local should only be called after the script which runs the test cases.

## Generating test reports
The BrowserStack Azure Pipelines extension enables you to embed the test results from BrowserStack into Azure Pipelines. We currently support viewing test reports in Azure Pipelines with a select set of languages and frameworks, which are Java with JUnit/TestNG, and Node with Protractor and WebdriverIO. The sections below describe how to configure your project to generate BrowserStack test reports.

### Java - TestNG and JUnit
A prerequisite for embedding test reports is that the Java project must be built with Maven and use either **TestNG** or **JUnit** for testing.

To configure your project:

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

### WebdriverIO
To configure your project:

Add `wdio-browserstack-reporter` as a dependency in your `package.json` file.

```json
{
  "dependencies": {
    "wdio-browserstack-reporter": "~0.1.1"
  }
}
```

Add `browserstack` as a reporter in your `conf` file.

```js
// wdio.conf.js
module.exports = {
    // ...
    reporters: ['browserstack'],
    reporterOptions: {
        browserstack: {
            outputDir: './'
        }
    },
    // ...
};
```

**outputDir** (_optional_): defines a directory location for storing browserstack report files.

[Here](https://github.com/browserstack/wdio-browserstack-reporter) is the link for the GitHub repository.

### Protractor
To configure your project:

Add `protractor-browserstack-reporter` as a dependency in your `package.json` file.

```json
{
  "dependencies": {
    "protractor-browserstack-reporter": "~0.1.1"
  }
}
```

Add `browserstack` as a reporter in your `conf` file.

```js
// conf.js
'plugins': [{
  'package': "browserstack-protractor-reporter"
}]
```

**outputDir** (_optional_): defines a directory location for storing browserstack report files.

[Here](https://github.com/browserstack/protractor-browserstack-reporter) is the link for the GitHub repository.

## Embedding test reports
Once you have completed the above steps, you can configure your Azure Pipelines setup to embed all the BrowserStack Selenium reports and logs.

1. Select Agent job 1 and click on the + icon. Type browserstack in the search box.
2. Hover on **BrowserStack Results** and click on the **Add** button. Click on **Control Options** and enable **Continue on error**.

![Embedding Test Reports](https://www.browserstack.com/images/static/docs/azure/embedding-reports.png)

Please make sure to add BrowserStack Results step after the step which runs your test cases.

With this integration, you can now view the results of your Selenium Webdriver tests within Azure Pipelines.

## Conclusion
This is how the test results look in Azure Pipelines:

![Conclusion](https://www.browserstack.com/images/static/docs/azure/conclusion.png)

By following the steps outlined in this guide, you should have a seamless integration between your existing automation in Azure Pipelines, your Selenium tests and the BrowserStack Selenium grid. This will help you leverage all the benefits of test automation and the scale and coverage offered by BrowserStack.
