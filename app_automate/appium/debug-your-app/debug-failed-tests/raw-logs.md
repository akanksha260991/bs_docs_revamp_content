# Raw Logs

#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Raw Logs capture all the details of your Appium test, including the capabilities you passed, the timestamps of every event/action which occurred in the test and details of every Appium command your executed during your test. You can download raw Appium raw logs using the 'Raw Logs' link on the Dashboard. You can also retrieve a link to download the Raw Logs by using our REST API for sessions. Raw Logs are availible only for Appium.

#### Retrieve Raw Logs URL link from session information using REST API:

```
curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/app-automate/sessions/<session-id>.json
```
