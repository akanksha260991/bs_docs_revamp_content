# Device Logs

#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Device Logs capture all the details of your Device test. You can view Device logs using the 'Device Logs' tab on the [App Automate Dashboard](https://app-automate.browserstack.com/dashboard). You can also retrieve a link to download the Device Logs by using our REST API. 

![Device-Logs](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/DeviceLogs-Espresso.png?raw=true "")






#### The Device logs can be retrieved using the REST API.

```
curl -u <username>:<access-key> https://api.browserstack.com/app-automate/espresso/builds/<build-id>/sessions/tests/<session-id>/devicelogs
```
