# Specifying the Uploaded App to be Tested


User can test their previously uploaded apps on BrowserStack, that were uploaded using a Custom ID. 

### browserstack.app_version

You can use 'browserstack.app_version' capability to configure your tests. Acceptable values are '-1', '-2' or so on. This will pick the latest-1 or latest-2 etc. version of your uploaded builds to install on your target device.

> Default: Latest uploaded app version under the custom_id

> Note: 'browserstack.app_version' capability can be used only if you use custom_id or shareable_id in the 'app' capability of your test. If you use app_url in the 'app' capability, Appium will install the app corresponding to the app_url ignoring the browserstack.app_version capability.

