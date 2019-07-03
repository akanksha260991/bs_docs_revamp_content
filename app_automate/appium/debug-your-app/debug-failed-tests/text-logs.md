# Text Logs

#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Text Logs are a comprehensive record of your Appium test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step. Text Logs are accessible from the Automate dashboard or via our REST API.

#### Access Text Logs from App Automate Dashboard:

![text logs](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/app-automate/test_logs@2x.png "text logs")

```
curl -u "USERNAME:ACCESS_KEY" https://api-cloud.browserstack.com/app-automate/builds/<build-id>/sessions/<session-id>/appiumlogs
```

#### Retrieve using REST API:

```
curl -u "USERNAME:ACCESS_KEY" https://api-cloud.browserstack.com/app-automate/builds/<build-id>/sessions/<session-id>/logs
```
