# Visual Logs

#### Leverage the full range of debugging tools provided by BrowserStack to easily debug your test automation for native and hybrid mobile apps.

Visual Logs automatically capture screenshots at every Appium command executed during your test. They help debug issues detected through your Appium tests. Visual logs help with debugging the exact step and the page where the failure occurred. They also help identify any layout or design related issues with your web pages on different browsers.

Visual Logs are disabled by default. In order to enable Visual Logs you will need to set **browserstack.debug** capability to **'true'**. Visual logs are available only for App Automate.

```
caps.setCapability("browserstack.debug", "true");
```

#### Access Visual Logs from App Automate Dashboard:

![Visual Logs](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/app-automate/visual-screen-shots@2x.png "Visual Logs")



