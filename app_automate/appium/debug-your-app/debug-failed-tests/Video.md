# Video Recording


#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Every Appium test run on BrowserStack is recorded exactly as it is executed on our remote machine. Video recordings are particularly helpful whenever a session fails as they help you retrace the steps which led to the failure. You can access the video recordings from App Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our REST API for sessions.

### Access video from Automate Dashboard:


![Video Recording](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/app-automate/video-recording@2x.png "")







### Retrieve video link from session information using REST API:

```
curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/app-automate/sessions/<session-id>.json
```


> Note: Video recording increases test execution time slightly. You can disable this feature by setting the browserstack.video capability to false.

```
caps.setCapability("browserstack.video", "false");
```

