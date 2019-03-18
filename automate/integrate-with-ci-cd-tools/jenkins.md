# Integrate BrowserStack Automate with Jenkins

## Introduction
Jenkins is an open-source Continuous Integration (CI) server, which automates the build and deploy process of your web applications. By running your Selenium test suite in Jenkins, you also automate testing as part of the build process. Add BrowserStack to the mix, and your web apps can be tested across 2000+ real devices and desktop browsers. BrowserStack lets you integrate your Selenium tests with Jenkins using a plugin that connects your Jenkins CI server to the BrowserStack Selenium Grid.

Use the BrowserStack Jenkins plugin to:

* Configure your BrowserStack credentials for your Jenkins jobs.
* Set up and tear down the BrowserStack Local binary for testing internal, development and staging environments.
* Embed BrowserStack test results, including video, logs, and screenshots in your Jenkins job results.

## Prerequisites
You need the following set up before you begin

* An existing Jenkins CI server (version 1.580.1+)
* A BrowserStack account

## Installing the Jenkins Plugin
To run your Selenium tests with Jenkins on BrowserStack, you will first need to download our [Jenkins plugin](https://wiki.jenkins-ci.org/display/JENKINS/BrowserStack+Plugin). Before installing the plugin, ensure you have the necessary privileges to administer your Jenkins installation. We recommend doing this when there are no active build jobs running on Jenkins.

1. Click on **Manage Jenkins** > **Manage Plugins**.
2. Click on the **Available Plugins** tab.
3. In the search box type _BrowserStack_.
4. Choose **BrowserStack** from the list of available plugins.

![Step 4 - Choose the BrowserStack plugin](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/jenkins/image1.png)

5. Check the box and install the plugin.
6. Once the plugin has been successfully installed you will be able to see it in the list of Installed plugins.

![Step 6 - See BrowserStack in the list of installed plugins](https://d98b8t1nnulk5.cloudfront.net/production/images/static/docs/jenkins/image2.png)

**Note:** If the BrowserStack plugin doesn’t show up in the list of Available plugins, click on the **Check Now** button to force Jenkins to pull the latest available plugins from Jenkins update center.

## Configuring BrowserStack credentials
Once you've installed the plugin, you will need to configure your BrowserStack credentials to complete the integration between Jenkins and your Selenium Webdriver tests. This can be done by either [using Jenkins UI](#using-jenkins-ui) or [using a Jenkinsfile](#using-a-jenkinsfile).

### Using Jenkins UI
Follow these steps to configure BrowserStack inside Jenkins:

1. Go to **Manage Jenkins** > **Configure System**.
2. Under the **BrowserStack** section, click on the **Add** button next to **BrowserStack Credentials**

![Using Jenkins UI - Step 2 - Adding BrowserStack Credentials](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/jenkins/image3.png)

3. Enter your BrowserStack Username and Access Key which you can find on your BrowserStack Account Settings page.

![Using Jenkins UI - Step 3 - Adding BrowserStack Credentials](https://d98b8t1nnulk5.cloudfront.net/production/images/static/docs/jenkins/image4.png)

4. Save your changes.

### Using a Jenkinsfile
Once you add your credentials as mentioned in the [above section](#using-jenkins-ui), Jenkins generates an ID which is used in the Jenkinsfile.

From the Jenkins home page, click on Credentials from the left menu. You can copy the ID for BrowserStack credentials.

![Copying BrowserStack Credentials in Jenkins](https://dgzoq9b5asjg1.cloudfront.net/production/images/static/docs/jenkins/bs-credentials.png)

Use the sample code in your Jenkinsfile:

```pip
browserstack(credentialsId: '<browserstackCredentialsID>') {
    // code for executing test cases
}
```

The above Jenkinsfile code block can also be generated with the help of [Pipeline Syntax generator tool](https://jenkins.io/doc/book/pipeline/getting-started/#snippet-generator) available in Jenkins.

To generate the step snippet with Snippet Generator:

1. Navigate to the Pipeline Syntax from a configured Pipeline, or at `<jenkins-ip>:<port>/pipeline-syntax`
2. Select the **browserstack:BrowserStack** in the **Sample Step** dropdown menu
3. Use the dynamically populated area below the **Sample Step** dropdown to configure the selected step.
4. Click **Generate Pipeline Script** to create a snippet of Pipeline which can be copied and pasted into a Pipeline.

![Generating pipeline script in Jenkins](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/jenkins/pipeline-generator.png)

## Configuring BrowserStack Local
[BrowserStack Local Testing](https://www.browserstack.com/local-testing) allows you to test web applications hosted on private, development, and internal servers. With the Local testing binary, you can create a secure, private connection between the BrowserStack Selenium Grid and your internal servers. The BrowserStack Jenkins plugin is responsible for:

* Downloading the BrowserStack local binary for every platform that the build job is running on
* Setting up and tearing down the secure tunnel.

**Note:** BrowserStack Local can only be configured per job and not via global configuration.

The BrowserStack Jenkins plugin sets the following environment variables:

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

### Using Jenkins UI
To enable BrowserStack Local follow these steps:

Under the **Build Environment** section check the box next to **BrowserStack Local**.

![Using Jenkins UI to set up BrowserStack local](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/jenkins/image6.png)

**Notes:**

1. If you are using an externally downloaded binary, you can enter its location at **BrowserStack Local Path**. If left empty, the plugin will automatically download the binary (recommended). This is recommended especially if you are using Jenkins in master-slave configurations, since the plugin will download the appropriate binary for the build agent OS.
2. Use **BrowserStack Local Options** to set any additional configuration options when running the binary (Full list of options available [here](https://www.browserstack.com/local-testing#modifiers))

Once you've integrated BrowserStack Local through the Jenkins plugin, you can run your Selenium tests on websites hosted on private, development, and internal servers safely and securely.

### Using a Jenkinsfile
To enable BrowserStack Local using a Jenkinsfile add **localConfig** option as shown in the sample code:

```
browserstack(credentialsId: '<browserstackCredentialsId>', localConfig: [localOptions: '<local-options>', localPath: '/path/to/local']) {
    // code for executing test cases
}
```

**Note:**

1. If you are using an externally downloaded binary, you can enter its location at **localPath**. If left empty, the plugin will automatically download the binary (recommended). This is recommended especially if you are using Jenkins in master-slave configurations, since the plugin will download the appropriate binary for the build agent OS.
2. Use **localOptions** to set any additional configuration options when running the binary (Full list of options available here) Once you've integrated BrowserStack Local through the Jenkinsfile, you can run your Selenium tests on websites hosted on private, development, and internal servers safely and securely.

## Generating test reports
The BrowserStack Jenkins plugin enables you to embed the test results from BrowserStack into Jenkins. We currently support viewing test reports in Jenkins with a select set of languages and frameworks, which are Java with JUnit/TestNG, and Node with Protractor and WebdriverIO. The sections below describe how to embed your BrowserStack test results in Jenkins.

### Java - TestNG and JUnit
A prerequisite for embedding test reports is that the Java project must be built with Maven and use either TestNG or JUnit for testing.

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

Add `browserstack` as a plugin in your `conf` file.

```js
// conf.js
'plugins': [{
  'package': "browserstack-protractor-reporter"
}]
```


**outputDir** (_optional_): defines a directory location for storing browserstack report files.

[Here](https://github.com/browserstack/protractor-browserstack-reporter) is the link for the GitHub repository.

## Embedding test reports
BrowserStack allows you to easily debug your Selenium Webdriver tests with features like Video recordings, Selenium logs, JavaScript Console logs, and Network logs. With our Jenkins plugin, you can view all the logs and reports generated by BrowserStack's Selenium grid right inside Jenkins.

Please make sure that **JUnit plugin** is installed in Jenkins.

The below steps for embedding test reports are same for Java - JUnit and TestNG, Protractor and WebdriverIO.

### Using Jenkins UI
Once you have completed the steps above, you can configure your Jenkins CI server to embed all the BrowserStack Selenium reports and logs in Jenkins.

1. Click on **Add post-build action** in **Post-build Actions**.

![Adding post-build action in Jenkins](https://www.browserstack.com/images/static/docs/jenkins/image7.png)

2. Click on **Publish JUnit test result report**

![Click on Publish JUnit test result report in Jenkins](https://www.browserstack.com/images/static/docs/jenkins/image8.png)

3. In the **Test report XMLs**,
  * Enter **target/surefire-reports/TEST-*.xml** for TestNG and JUnit
  * Enter **\*/browserstack-reports/REPORT-.xml** for WebdriverIO and Protractor

4. In the **Additional test report features** section, add **Embed BrowserStack Report**.

![Embedding BrowserStack report in Jenkins](https://www.browserstack.com/images/static/docs/jenkins/image10.png)


With this integration, you can now view the results of your Selenium Webdriver tests within Jenkins.

### Using a Jenkinsfile
Use the code in your Jenkinsfile

```
junit testDataPublishers: [[$class: 'AutomateTestDataPublisher']], testResults: 'target/surefire-reports/TEST-*.xml'
```
This is a post build step and should be added after browserstack block in the Jenkinsfile. With this integration, you can now view the results of your Selenium Webdriver tests within Jenkins.

### Conclusion
By following the steps outlined in this guide, you should have a seamless integration between your existing automation in Jenkins, your Selenium tests and the BrowserStack Selenium grid. This will help you leverage all the benefits of test automation and the scale and coverage offered by BrowserStack.
