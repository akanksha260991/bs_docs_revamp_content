# Console Logs#

Console Logs capture the browser's console output at various steps of the test to troubleshoot JavaScript issues.


Console Logs are by default **enabled** in tests, with log level set to **errors**.<br/>To set different log levels, you need to use the capability ```browserstack.console``` with possible values

- ```disable```
- ```errors```
- ```warnings```
- ```info```
- ```verbose```


<br/>
You can access console logs in a session on [Automate Dashboard](https://automate.browserstack.com).

<br/>
You can also retrieve Console Logs using [REST API](https://www.browserstack.com/automate/rest-api#rest-api-sessions):<br/>
```curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/automate/builds/<build-id>/sessions/<session-id>/consolelogs
```
<br/>
**Note:** Currently, console logs are available only for Selenium tests on Chrome & Firefox browsers and Android & iOS devices.