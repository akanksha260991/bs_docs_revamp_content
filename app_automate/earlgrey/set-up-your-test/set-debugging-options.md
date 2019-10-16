# Set debugging options

You can use various logging options to debug your failed test cases and fix them with ease.

## Instrumentation logs
These are a comprehensive record of your test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Instrumentation Logs are accessible from the [App Automate dashboard](https://app-automate.browserstack.com/dashboard).

You don't need to set any options for instrumentation logs, as these are enabled by default, and cannot be disabled.

## Video logs
Every test run on the BrowserStack is recorded exactly as it is executed on our remote machine. This feature is particularly helpful whenever a browser test fails, so you can see the entire test in action. You can access these videos from the [App Automate dashboard](https://app-automate.browserstack.com/dashboard) for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our [REST API](https://www.browserstack.com/app-automate/rest-api?framework=xcuitest).

Video logs are enabled by default. Note that video recording increases the text execution time slightly. You can disable them by setting the 'video' parameter in to false while executing the Espresso test.

| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`video`|Optional|Set this parameter if you want to enable the video of the test run. By default it's set to `true`|

## Network logs
Network Logs capture the apps performance data such as network traffic, latency, HTTP requests and responses in the HAR format. You can download network logs using both the [App Automate dashboard](https://app-automate.browserstack.com/dashboard), as well as the [REST API to access network logs](https://www.browserstack.com/app-automate/rest-api?framework=xcuitest). You can visualize HAR files using the [HAR Viewer](http://www.softwareishard.com/har/viewer/).

Network Logs are disabled by default. To enable Network Logs et the networkLogs parameter to true while executing the test. By default the value is set to false.

| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`networkLogs`|Optional|Set this parameter if you want to enable the Network Logs. By default its `false`.|


## Device logs
Get the device logs for your test executions. To enable Device Logs set the **deviceLogs** parameter to true while executing the test. 

| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`deviceLogs`|Optional|Set this parameter if you want to enable the Device Logs |
