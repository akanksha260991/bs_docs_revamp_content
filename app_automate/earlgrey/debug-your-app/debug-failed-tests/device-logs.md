# Device Logs

#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Device Logs capture all the details of your Device test. You can view these logs using the **Device Logs** tab in the [App Automate Dashboard](https://app-automate.browserstack.com/dashboard). You can also retrieve a link to download the Device Logs by using our REST API. 

To enable the Device Logs use the API parameter `deviceLogs` with the value `true` in the execute test API:
```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/earlgrey/build" -d "{\"devices\": [\"iPhone 8 Plus-11\"], \"appDir\": \"bs://<hashed appid>\", \"deviceLogs\" : \"true\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS-KEY"
```


### Raw Device Logs

![Device-Logs](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/Screenshot%202019-10-17%20at%2012.10.08%20PM.png)


<br>

### The Device logs can be retrieved using the REST API.

```bash
curl -u USERNAME:ACCESS-KEY https://api.browserstack.com/app-automate/earlgrey/builds/<build-id>/sessions/tests/<test-id>/devicelogs
```
