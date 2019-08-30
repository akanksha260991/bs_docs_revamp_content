# Network Logs


#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Network Logs capture the app's performance data such as network traffic, latency, HTTP requests and responses in the HAR format. You can leverage Network logs to get a clear picture of all the requests your app is making, and the time taken for each of these requests. You can download network logs using the REST API or via the [App Automate Dashboard](https://app-automate.browserstack.com/dashboard). 


Network Logs are disabled by default. To enable Network Logs use the capability **browserstack.networkLogs** with the value **'true'**

> "browserstack.networkLogs" : "true"




----
#### Network Logs from App Automate Dashboard:
![Network Logs](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/Screenshot%202019-08-30%20at%208.18.14%20PM.png?raw=true "")



#### Raw Network Logs
![Network Logs](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/Screenshot%202019-08-30%20at%208.10.45%20PM.png?raw=true "")

-----
#### Retrieve Network Logs using REST API:

```java
curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/app-automate/espresso/builds/<build-id>/sessions/tests/<test-id>/networklogs
```

