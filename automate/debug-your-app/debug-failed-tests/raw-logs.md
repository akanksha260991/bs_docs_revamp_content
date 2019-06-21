# Raw Selenium Logs #

Raw Logs capture all the details of your Selenium or JavaScript test, including the capabilities you passed, the timestamps of every event/action which occurred in the test and details of every Selenium command your executed during your test.

You can download raw Selenium logs using the 'Raw Logs' link in a session inside [Automate Dashboard](https://automate.browserstack.com).

<br/>
You can also retrieve a link to download the Raw Logs by using REST API for sessions:<br/>
```curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/automate/sessions/<session-id>.json```