# Mark Tests as Pass or Fail

You can mark your Appium tests as **passed** or **failed**, using the PUT. Also, a reason can be specified for the failure. The REST API for the same:

```bash
curl -u "USERNAME:ACCESS_KEY" -X PUT -H "Content-Type: application/json" -d "{\"status\":\"<new-status>\", \"reason\":\"<reason text>\"}" https://api-cloud.browserstack.com/app-automate/sessions/<session-id>.json
```

