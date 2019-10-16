# Screenshots

You can save the screenshots automatically captured by XCode. Screenshots can be rendered by accessing the rawlogs using the XCUITest sessions API

To enable the screenshots set the API parameter `debugscreenshots` to true

| Parameter  | Value       |
| ------------- |-------------|
| ```debugscreenshots```| ```true```|


#### Command
```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/build" -d "{\"devices\": [\"iPhone 8 Plus-11\"], \"app\": \"bs://<hashed-id>\", \"deviceLogs\" : \"true\", \"debugscreenshots\" : \"true\", \"networkLogs\" : \"true\", \"testSuite\": \"bs://<hashed-test-id>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS-KEY"
```

<br>

## App Automate Dashboard - Visual Logs
You can see the screenshots captured under the **Visual Logs** Tab in the test details page of Dashboard.

![Visual Logs](https://github.com/akanksha260991/bs_docs_revamp_content/blob/master/Screenshot%202019-10-17%20at%2012.01.25%20AM.png)
