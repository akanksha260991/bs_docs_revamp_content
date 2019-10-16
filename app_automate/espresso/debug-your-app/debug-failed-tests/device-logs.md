# Device Logs

#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Device Logs capture all the details of your Device test. You can view these logs using the **Device Logs** tab in the [App Automate Dashboard](https://app-automate.browserstack.com/dashboard). You can also retrieve a link to download the Device Logs by using our REST API. 

To enable the Device Logs use the API parameter `deviceLogs` with the value `true` in the execute test API:
```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/espresso/build" -d \ "{\"devices\": [\"Samsung Galaxy S8-7.0\"], \"app\": \"bs://<hashed appid>\", \"deviceLogs\" : true, \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS-KEY"
```


### Device Logs on Dashboard:

![Device-Logs](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/DeviceLogs-Espresso.png?raw=true "")




### The Device logs can be retrieved using the REST API.

```bash
curl -u USERNAME":"ACCESS-KEY https://api.browserstack.com/app-automate/espresso/builds/<build-id>/sessions/tests/<session-id>/devicelogs
```
