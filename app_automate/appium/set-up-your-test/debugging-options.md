# Set Debugging Options

You can enable/disable different debugging options for your Appium tests on BrowserStack. The capabilities available are:

***

1. Device Logs
2. Appium Logs
3. Visual Logs
4. Video
5. Network Logs
***


### Device Logs
Use the capability named browserstack.deviceLogs,  if you want to capture device logs for your test. The values are true/false. By default, the value is set to true. The example of setting this capability:


> "browserstack.deviceLogs": "false"


--


### Appium Logs
Use the capability named **browserstack.appiumLogs**,  if you want to capture raw appium logs for your test. The values are true/false. By default, the value is set to true. The example of setting this capability:

> "browserstack.appiumLogs": "false"


--


### Visual Logs
Use the capability named **browserstack.debug**,  if you want to generate screenshots at various steps in your test. The values are true/false. By default, the value is set to false. The example of setting this capability:

> "browserstack.debug": "true"


--


### Video Logs
Use the capability named **browserstack.video**, if you want to generate screenshots at various steps in your test. The values are true/false. By default, the value is set to true. The example of setting this capability:

> "browserstack.video": "true"


--


### Network Logs
Use the capability named **browserstack.networkLogs**, if you want to capture network logs for your test. The values are true/false. By default, the value is set to false. The example of setting this capability:

> "browserstack.networkLogs": "true"




