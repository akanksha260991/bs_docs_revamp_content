# App Automate Network Logs

Once the user has completed the session, the network logs for your Appium Sessions are available to you in HAR (HTTP Archive) format. 

After the test completes, you can access the logs through the BrowserStack App Automate Dashboard, or with the REST API:

```
curl -u "USERNAME:ACCESS_KEY" https://api-cloud.browserstack.com/app-automate/builds/<build-id>/sessions/<session-id>/networklogs
```
