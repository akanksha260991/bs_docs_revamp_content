# Video Recording

Every test run on the BrowserStack is recorded exactly as it is executed on our remote device. This feature is particularly helpful whenever a test fails. You can access videos from App Automate Dashboard for each session. 

You can also download the videos from the Dashboard or retrieve a link to download the video using our REST API.

> Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.


```
caps.setCapability("browserstack.video", "false");
```

