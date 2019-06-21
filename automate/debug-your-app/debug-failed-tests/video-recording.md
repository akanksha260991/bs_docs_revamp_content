# Video Recording #

Every Selenium or JavaScript test run on BrowserStack is recorded exactly as it is executed on our remote machine. Video recordings are particularly helpful whenever a browser test fails as they help you retrace the steps which led to the failure. You can access the video recordings from Automate Dashboard for each session. You can also download the videos from the Dashboard or retrieve a link to download the video using our REST API for sessions.

Access video from Automate Dashboard:

![browserstack automate test video](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/debugging/video@2x.jpg)


Retrieve video link from session information using REST API:
```curl -u "USERNAME:ACCESS_KEY" https://api.browserstack.com/automate/sessions/<session-id>.json```
