# Selenium Logs #

Selenium Logs captures the raw logs of Selenium JAR for your test execution. With Selenium logs you can debug the test in case of any exceptions.

You can access Selenium Logs in a session on [Automate Dashboard](https://automate.browserstack.com).

You can also download logs using the REST API:<br/>

```curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/automate/builds/<build-id>/sessions/<session-id>/seleniumlogs
```