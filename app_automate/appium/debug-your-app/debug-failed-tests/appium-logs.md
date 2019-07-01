# App Automate Text Logs

Once the user has completed the session, the logs for the Appium Sessions can be viewed in the text format. This feature enables the user to debug their app in case their is any failure. 

The text logs can be retrieved using the REST API.

```
curl -u "USERNAME:ACCESS_KEY" https://api-cloud.browserstack.com/app-automate/builds/<build-id>/sessions/<session-id>/appiumlogs
```

> Note: The text logs may have valuable data that can help user understand any problem that exists in the test execution.
