# Network Logs #

Network Logs capture the browser's performance data such as network traffic, latency, HTTP requests and responses in the HAR format. You can leverage Network logs to get a clear picture of all the requests your web page is making, and the time taken for each of these requests. You can download network logs using the REST API or via the Automate Dashboard. You can visualize HAR files using the [HAR Viewer](http://www.softwareishard.com/har/viewer/).


Network Logs are disabled by default. To enable Network Logs use the capability ```browserstack.networkLogs``` with the value ```true```.


Retrieve Network Logs using REST API:

```curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/automate/builds/<build-id>/sessions/<session-id>/networklogs```

<br/>
**Note:** You may experience minor reductions in performance when testing with Network Logs turned on with Desktop sessions.