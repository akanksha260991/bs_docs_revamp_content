# Integrate Appium with Jenkins

#### Integrate your Appium test suite with Jenkins and the BrowserStack device cloud for native and hybrid apps using our plugin.

## Introduction
Jenkins is an open-source Continuous Integration (CI) server, which automates the build and deploy process of your web applications. By running your Selenium test suite in Jenkins, you also automate testing as part of the build process. Add BrowserStack to the mix, and your web apps can be tested across 2000+ real devices and desktop browsers. BrowserStack lets you integrate your Selenium tests with Jenkins using a plugin that connects your Jenkins CI server to the BrowserStack Selenium Grid.

Use the BrowserStack Jenkins plugin to:

* Configure your BrowserStack credentials for your Jenkins jobs.
* Upload your app build to the BrowserStack servers.
* Set up and tear down the BrowserStack Local binary for testing internal, development and staging environments.
* Embed BrowserStack test results, including video, logs, and screenshots in your Jenkins job results.

## Prerequisites
You need the following set up before you begin

* An existing Jenkins CI server (version 1.653+)
* A BrowserStack account


## Installing the Jenkins Plugin
To run your Selenium tests with Jenkins on BrowserStack, you will first need to download our Jenkins plugin. Before installing the plugin, ensure you have the necessary privileges to administer your Jenkins installation. We recommend doing this when there are no active build jobs running on Jenkins.

1. Click on **Manage Jenkins** > **Manage Plugins**

2. Click on the **Available Plugins** tab

3. In the search box type **BrowserStack**

4. Choose **BrowserStack** from the list of available plugins

![Using Jenkins UI - Step 4 - Adding BrowserStack Credentials](https://www.browserstack.com/images/static/docs/jenkins/image1.png)

5. Check the box and install the plugin

6. Once the plugin has been successfully installed you will be able to see it in the list of Installed plugins

![Using Jenkins UI - Step 6 - Adding BrowserStack Credentials](https://www.browserstack.com/images/static/docs/jenkins/image2.png)

> Note: If the BrowserStack plugin doesn’t show up in the list of Available plugins, click on the Check Now button to force Jenkins to pull the latest available plugins from Jenkins update center.

## Configuring your BrowserStack credentials
Once you've installed the plugin, you will need to configure your BrowserStack credentials to complete the integration between Jenkins and your Appium tests. Follow these steps to configure BrowserStack inside Jenkins:

1. Go to Manage Jenkins > Configure System

2. Under the BrowserStack section, click on the Add button next to BrowserStack Credentials

![Using Jenkins UI - Step 2 - Adding BrowserStack Credentials](https://www.browserstack.com/images/static/docs/jenkins/image3.png)

3. Enter your BrowserStack Username and Access Key which you can find on your BrowserStack Account Settings page

![Using Jenkins UI - Step 3 - Adding BrowserStack Credentials](https://www.browserstack.com/images/static/docs/jenkins/image4.png)

4. Save your changes


### Configuring app upload step
Next you will need upload your app to the BrowserStack servers and for that you will need to configure the app upload build step. Follow these steps to configure a build step to upload your app to the BrowserStack servers:

1. Go to build job > Configure

2. In the Build section click on Add build step

3. Select Upload app to BrowserStack

![Using Jenkins UI](https://dgzoq9b5asjg1.cloudfront.net/production/images/static/docs/jenkins/upload-app@2x.png)

4. Enter the path to your app file on the local jenkins server and click save

![Using Jenkins UI](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/jenkins/app-path@2x.png)


### Configure BrowserStack credentials in TestSuite

The BrowserStack Jenkins plugin sets the following environment variables:

```bash
BROWSERSTACK_USERNAME
BROWSERSTACK_ACCESS_KEY
BROWSERSTACK_APP_ID
BROWSERSTACK_LOCAL
BROWSERSTACK_LOCAL_IDENTIFIER
```


Use these environment variables to set the **DesiredCapabilities** in your tests. For example:

```java
String username = System.getenv("BROWSERSTACK_USERNAME");
String accessKey = System.getenv("BROWSERSTACK_ACCESS_KEY");
String browserstackLocal = System.getenv("BROWSERSTACK_LOCAL");
String browserstackLocalIdentifier = System.getenv("BROWSERSTACK_LOCAL_IDENTIFIER");
String app = System.getenv("BROWSERSTACK_APP_ID");

DesiredCapabilities capabilities = new DesiredCapabilities();
capabilities.setCapability("device", "Samsung Galaxy S8");
capabilities.setCapability("app", app);
capabilities.setCapability("browserstack.local", browserstackLocal);
capabilities.setCapability("browserstack.localIdentifier", browserstackLocalIdentifier);
driver = new RemoteWebDriver(new URL("https://" + username + ":" + accessKey + "@hub.browserstack.com/wd/hub"), capabilities);
```

> Note: If you are using BrowserStack Local, you must pass browserstack.local and browserstack.localIdentifier capabilities to test on your local development servers.

To enable BrowserStack Local follow these steps:

1. Under the Build Environment section check the box next to BrowserStack Local

![Using Jenkins UI](https://www.browserstack.com/images/static/docs/jenkins/image6.png)

2. If you are using an externally downloaded binary, you can enter its location at BrowserStack Local Path. If left empty, the plugin will automatically download the binary (recommended). This is recommended especially if you are using Jenkins in master-slave configurations, since the plugin will download the appropriate binary for the build agent OS.
Use BrowserStack Local Options to set any additional configuration options when running the binary. Full list of options available [here](https://www.browserstack.com/local-testing#modifiers)


Once you've integrated BrowserStack Local through the Jenkins plugin, you can run your Appium tests for mobile apps with servers hosted on private, development, and internal staging safely and securely.

## Generating and Embedding test reports

BrowserStack allows you to easily debug your Appium tests with features like Video recordings, Text logs and Device logs. With our Jenkins plugin, you can view all the logs and reports generated by BrowserStack right inside Jenkins. At this point, embedding test reports only works with a select set of frameworks. The prerequisites are:

1. Project must be a Java project

2. Project must be built with Maven and use either TestNG or JUnit for testing

3. Jenkins JUnit plugin must be installed

![Using Jenkins UI](https://www.browserstack.com/images/static/docs/jenkins/image9.png)


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
</pluginRepositories
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

Once you have completed the steps above, you can configure your Jenkins CI server to embed all the BrowserStack Appium reports and logs in Jenkins.

1. Click on **Add post-build action** in **Post-build Actions**

![Using Jenkins UI](https://www.browserstack.com/images/static/docs/jenkins/image7.png)

2. Click on **Publish JUnit test result report**

![Using Jenkins UI](https://www.browserstack.com/images/static/docs/jenkins/image8.png)

3. In the **Test report XMLs**, enter **target/surefire-reports/TEST-\*.xml**


4. In the **Additional test report features** section, add **Embed BrowserStack Report**

With this integration, you can now view the results of your Appium tests within Jenkins

## Conclusion
By following the steps outlined in this guide, you should have a seamless integration between your existing automation in Jenkins, your Appium tests and the BrowserStack device cloud. This will help you leverage all the benefits of test automation and the scale and coverage offered by BrowserStack.

