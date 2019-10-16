# Accept Insecure Certificate

If you want to use self signed certificates to test the iOS app, the `acceptInsecureCerts` needs to be `true` in your XCUITests execution command. By default its set to `false` on BrowserStack.
This will help to avoid any invalid certificate errors while using self-signed certificate. 



| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`acceptInsecureCerts`|Optional| Set this parameter to true to avoid invalid certificate errors while using self-signed certificate to test your app|



#### Command

```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/build" -d "{\"devices\": [\"iPhone 8 Plus-11\"], \"app\": \"bs://<hashed appid>\", \"acceptInsecureCerts\" : \"true\", \"deviceLogs\" : \"true\", \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "<username>:<access-key>"
```
