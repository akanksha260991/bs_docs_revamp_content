# Get session ID

In cases where you want to uniquely identify the current test, you might want to use the session ID that BrowserStack generates automatically for each of your test runs.

You can use these sessions IDs to internally map with your test cases, or logging, or automatically raising a bug reports.

You can use the WebDrivers's API to retrieve BrowserStack's session ID as follows:

```java
// Start a remote browser with the desired capabilities
WebDriver driver = new RemoteWebDriver(new URL(URL), caps);

// ... run some tests

// Get the session ID
driver.getSessionId();

// ... do something with the session ID

// ... continue with your tests
```
