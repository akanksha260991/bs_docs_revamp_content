# Network Logs


#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Network Logs capture the app's performance data such as network traffic, latency, HTTP requests and responses in the HAR format. You can leverage Network logs to get a clear picture of all the requests your app is making, and the time taken for each of these requests. 

Network Logs are disabled by default. To enable Network Logs update the API parameter `networkLogs` with the value `true` in your execute test API:

```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/build" -d \ "{\"devices\": [\"Samsung Galaxy S8-7.0\"], \"app\": \"bs://<hashed appid>\", \"deviceLogs\" : true, \"networkLogs\" : true, \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS-KEY"
```



You can download network logs using the REST API or via the [App Automate Dashboard](https://app-automate.browserstack.com/dashboard) under the Network Logs tab


## Raw Network Logs:
![Network Logs](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/Screenshot%202019-08-30%20at%208.10.45%20PM.png?raw=true "")



### Retrieve Network Logs using REST API:

```bash
curl -u USERNAME:ACCESS-KEY https://api.browserstack.com/app-automate/xcuitest/builds/<build-id>/sessions/tests/<test-id>/networklogs
```

