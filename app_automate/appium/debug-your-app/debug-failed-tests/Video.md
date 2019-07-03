# Video Recording


#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Every Appium, Espresso, XCUITest or EarlGrey test run on BrowserStack is recorded exactly as it is executed on our remote machine. Video recordings are particularly helpful whenever a browser test fails as they help you retrace the steps which led to the failure. You can access the video recordings from App Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our REST API for sessions.

You can also acces/download the video from the App Automate Dashboard:


![Video Recording](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/app-automate/video-recording@2x.png "")






> Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
caps.setCapability("browserstack.video", "false");
```

