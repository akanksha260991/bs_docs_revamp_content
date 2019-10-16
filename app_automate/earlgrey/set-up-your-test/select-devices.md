# Select Devices
Use the 'devices' parameter while running your XCUITest in order to select the device and OS version you want to run your tests on.

| Parameters | Required/Optional | Description |
| ---------- | ----------- | --------------- |
|`devices`| Required | list of Device and OS version you want to run your tests on. Acceptable format is -. Example: iPhone 8 Plus-11 |

Complete list of all the available devices can be found [here](https://www.browserstack.com/list-of-browsers-and-platforms/app_automate)

### Specify Device

You need to specify the device while executing the test on BrowserStack: 

```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/earlgrey/build" -d "{\"devices\": [\"iPhone XS-12\"], \"appDir\": \"bs://<hashed appid>\", \"deviceLogs\" : \"true\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS-KEY"
```
