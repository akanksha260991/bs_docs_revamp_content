# Select the Automation Engine


There are certain BrowserStack specific capabilities available to configure your test. You can set the Automation Engine to use for your Appium Tests.


### automationName
In order to set the automation engine, use the capability **automationName**. The options available for it are UIAutomator2, XCUITest, Appium. The Default for Android is UIAutomator2 and Default for iOS is XCUITest.  Here is an example to use the capability: 

> "automationName" : "UIAutomator2"
