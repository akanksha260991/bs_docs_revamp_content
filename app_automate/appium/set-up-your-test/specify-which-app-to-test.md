# Specifying the Uploaded App to be Tested

Appium provides a series of capabilities that you can set for the Appium version you are running. Appium server on the BrowserStack will receive the capability you set on the client side. User can use BrowserStack specific capabilities to configure the tests.

To test the previously uploaded apps on BrowserStack, that were uploaded using a Custom ID, use the capability: 

### browserstack.app_version

You can use 'browserstack.app_version' capability to configure your tests. Acceptable values are '-1', '-2' or so on. This will pick the latest-1 or latest-2 etc. version of your uploaded builds to install on your target device.

> Default: Latest uploaded app version under the custom_id

> Note: 'browserstack.app_version' capability can be used only if you use custom_id or shareable_id in the 'app' capability of your test. If you use app_url in the 'app' capability, Appium will install the app corresponding to the app_url ignoring the browserstack.app_version capability.

