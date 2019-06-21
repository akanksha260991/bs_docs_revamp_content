# Text Logs #

Text Logs are a comprehensive record of your Selenium or JavaScript test. They are used to identify all the steps executed in the test and troubleshoot errors for the failed step.

You can access Text Logs in a session on [Automate Dashboard](https://automate.browserstack.com).

You can also download text logs using the REST API:<br/>
```curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/automate/builds/<build-id>/sessions/<session-id>/logs```