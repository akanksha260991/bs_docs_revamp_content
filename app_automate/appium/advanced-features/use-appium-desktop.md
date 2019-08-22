# Using Appium Desktop with BrowserStack
#### Inspect your app elements and interact with them on the BrowserStack device cloud using Appium Desktop


### Introduction
Appium Desktop is an open source app for Mac, Windows, and Linux which gives you the power of Appium automation server in a flexible UI. It is a combination of a few Appium-related tools:

* A graphical interface for the Appium Server to set options, start/stop the server, see logs, etc. You also don't need to use Node/NPM to install Appium, as the Node runtime comes bundled with Appium Desktop.

* An Inspector that can be used to look up the app elements, and perform basic interactions with them. This is useful as a way to learn about Appium or the app so users can write tests for it without requiring to have access to the app code.
BrowserStack integration with Appium Desktop allows you to inspect your app elements remotely using the BrowserStack device cloud. Download the latest release of Appium Desktop from the [Appium release](https://github.com/appium/appium-desktop/releases/tag/v1.6.1) page on GitHub.



### Usage Instructions
Refer the instructions on [Appium Desktop GitHub page](https://github.com/appium/appium-desktop#usage-instructions) to start using Appium Desktop. Follow below steps to use BrowserStack with Appium Desktop:

**Step 1: Upload your app**

Upload your Android app (.apk file) or iOS app (.ipa file) to the BrowserStack servers using the REST API.

```bash
curl -u "akanksha48:Gj9ztcdyupnWkwjmuTis" \
  -X POST "https://api-cloud.browserstack.com/app-automate/upload" \
  -F "file=@/path/to/app/file/Application-debug.apk"
  ```

Please note the App URL (bs://<hashed appid>) returned in the response. The response of the call will be like:

```bash
{"app_url":"bs://<hashed appid>"}
```


**Step 2: Configure BrowserStack credentials on Appium Desktop**

In the BrowserStack tab on Appium Desktop window enter the BrowserStack Username and Access Key which you can find on your BrowserStack [Account Settings](https://www.browserstack.com/accounts/settings) page.


**Step 3: Configure Desired Capabilities and launch**

Configure BrowserStack capabilities in the "Desired Capabilities" tab on Appium Desktop and "Start Session".

> Note: Refer [Capabilities](https://www.browserstack.com/app-automate/capabilities) page for all the App Automate supported capabilities.


![Appium Desktop](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/app-automate/integrations/image1@2x.png)

On successful connection with the BrowserStack device you will be able to inspect elements of your app and record the session using the BrowserStack device on the cloud.

![Appium Desktop](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/app-automate/integrations/image2@2x.png)


## Conclusion
Using Appium Desktop with BrowserStack makes its very convenient for you to inspect your app elements and write the automation tests for your app.

Note: You will still need to have valid App Automate plan to use Appium Desktop. In the App Automate free plan your free minutes will be deducted for the time you use Appium Desktop and you will be able to use only the free devices for testing. Refer to the App Automate [pricing](https://www.browserstack.com/accounts/subscriptions?tab=mobile-app-plans-tab) for more details.

