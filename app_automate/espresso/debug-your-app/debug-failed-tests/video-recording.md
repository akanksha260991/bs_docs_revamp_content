# Video Recording


#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Espresso tests run on BrowserStack is recorded exactly as it is executed on our remote machine. Video recordings are particularly helpful whenever a test fails as they help you retrace the steps which led to the failure. You can access the video recordings from App Automate Dashboard for each session. You can also download the videos from the [App Automate Dashboard](https://app-automate.browserstack.com/dashboard) or retrieve a link to download the video using our REST API for sessions.

Video is enabled by default. To disable Video use the API parameter `video` with the value `false` in the execute test API:

```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/espresso/build" -d \ "{\"devices\": [\"Samsung Galaxy S8-7.0\"], \"app\": \"bs://<hashed appid>\", \"deviceLogs\" : true, \"false\" : true, \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS-KEY"
```


### Video from Dashboard:


![Video Recording](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/Espresso-Dashboard.png?raw=true "")



