# Integrate BrowserStack Automate with TeamCity

## Introduction

This plugin helps you:
* Manage your BrowserStack credentials globally or per build job.
* Set up and teardown [BrowserStack Local](https://www.browserstack.com/local-testing) for testing internal, dev or staging environments.
* Embed [BrowserStack Automate](https://automate.browserstack.com/) reports in your TeamCity job results.

## Requirements
For viewing BrowserStack Automate report in your TeamCity test results:

* Project must be a Java project.
* Project must be built with Maven and use either TestNG or JUnit for testing.

## Installing the Plugin
1. Go to **Server Administration** and click **Plugins List**

![Go to Server Administration and click Plugins List](https://www.browserstack.com/images/static/docs/teamcity/image1.png)

2. Click on **upload plugin zip** and upload our [plugin](https://plugins.jetbrains.com/teamcity/plugin/9351-browserstack)

![Click on upload plugin zip and upload our plugin](https://www.browserstack.com/images/static/docs/teamcity/image2.png)

3. Restart TeamCity. You should now see BrowserStack in your list of available Plugins

![Restart TeamCity. You should now see BrowserStack in your list of available Plugins](https://www.browserstack.com/images/static/docs/teamcity/image3.png)

## Configuring Projects
1. Click on **Build features**
2. Click **Add build feature** and select **BrowserStack** from the list

![Click Add build feature and select BrowserStack from the list](https://www.browserstack.com/images/static/docs/teamcity/image4.png)

3. Add your BrowserStack USERNAME and ACCESS_KEY, and select the options from the form

![Add your BrowserStack USERNAME and ACCESS_KEY](https://www.browserstack.com/images/static/docs/teamcity/image5.png)

4. Save your changes

## Configuring Tests
Following environment variables are set by this TeamCity plugin:

```bash
BROWSERSTACK_USERNAME
BROWSERSTACK_ACCESS_KEY
BROWSERSTACK_LOCAL
BROWSERSTACK_LOCAL_IDENTIFIER
```

Use these environment variables to set `DesiredCapabilities` in your test. Here is a sample Java code:

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

## Generating test reports (for JUNit and TestNG only)
This step is required for viewing the BrowserStack Automate Report in your TeamCity job results. Currently, JUnit and TestNG Maven based projects are supported.

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
You must define the maven properties, `jdk.source.version` and `jdk.target.version` to match the Java versions of your project.

## Test Results

1. Go to **Project Overview**
2. Select **BrowserStack** tab
3. Click on an individual test to view its BrowserStack Automate report
