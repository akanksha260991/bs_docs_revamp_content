# Visual Logs

Visual Logs automatically capture the screenshots generated at every Appium command run through your test suite. Visual logs help with debugging the exact step and the page where failure occurred. They also help identify any layout or design related issues with your app pages on different devices.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to 'true':

The text logs can be retrieved using the REST API.

```
caps.setCapability("browserstack.debug", "true");
```


