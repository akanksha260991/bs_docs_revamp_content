# Set debugging options

You can use various logging options to debug your failed test cases and fix them with ease.

## Text logs
These are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible both from the [Automate dashboard](https://automate.browserstack.com/dashboard), as well as via our [REST API to access text logs](https://www.browserstack.com/automate/rest-api).

You don't need to set any options for text logs, as these are enabled by default, and cannot be disabled.

## Visual logs
These contain the screenshots that are auto-generated at every Selenium command that your code executes. Visual logs help with debugging the exact step and how the page was rendered when the failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual logs are disabled by default. You can enable them by using the `browserstack.debug` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
| `browserstack.debug`  | Enable visual logs? | A boolean. Defaults to `False` <br/><br/> `True` if you want to enable the visual logs. `False` otherwise. |

For example, this is how you enable visual logs in Java:

```java
// Enabling visual logs

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserstack.debug", "true");
```

## Video logs
Every test run on the BrowserStack Selenium grid is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails, so you can see the entire test in action. You can access these videos from the [Automate dashboard](https://automate.browserstack.com/dashboard) for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/automate/rest-api).

Video logs are enabled by default. Note that video recording increases the text execution time slightly. You can disable them by using the `browserstack.video` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
| `browserstack.video`  | Enable video recording? | A boolean. Defaults to `True` <br/><br/> `True` if you want to enable the video recording. `False` otherwise. |

For example, this is how you disable video logs in Java:

```java
// Disabling video logs

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserstack.video", "false");
```

## Console logs
Console Logs capture the browser's JS console output at various steps of the test to troubleshoot JavaScript issues. You can retrieve Console Logs using both the [Automate dashboard](https://automate.browserstack.com/dashboard), as well as the [REST API to access text logs](https://www.browserstack.com/automate/rest-api). Currently, this works only for Chrome browsers, and we will roll this out to other browsers soon.

Console logs are enabled by default and are set to `errors`. You can disable them, or change verbosity options by using the `browserstack.console` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
| `browserstack.console`  | Level of console logs to capture | A string. Defaults to `errors` <br/><br/> `disabled` to disable console logs.<br/> `errors` to capture errors.<br/> `warnings` to capture warnings and errors.<br/> `info` to capture info, warnings and errors.<br/> `verbose` to capture everything|

For example, this is how you disable console logs in Java:

```java
// Disabling console logs

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserstack.console", "disabled");
```

If you want to change the console logs level to `info`, this is how you do it in Java:

```java
// Setting console logs level to info

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserstack.console", "info");
```

## Network logs
Network Logs capture the browser's performance data such as network traffic, latency, HTTP requests and responses in the HAR format. You can download network logs using both the [Automate dashboard](https://automate.browserstack.com/dashboard), as well as the [REST API to access text logs](https://www.browserstack.com/automate/rest-api). You can visualize HAR files using the [HAR Viewer](http://www.softwareishard.com/har/viewer/).

Network Logs are disabled by default. To enable Network Logs use the `browserstack.networkLogs` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
| `browserstack.networkLogs`  | Enable network logs? | A boolean. Defaults to `False` <br/><br/> `True` if you want to enable network logs. `False` otherwise. |

For example, this is how you enable network logs in Java:

```java
// Enabling network logs

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserstack.networkLogs", "true");
```
