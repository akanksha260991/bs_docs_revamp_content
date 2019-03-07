# Take screenshots

In cases where you would want to automatically take screenshots, be it to file a bug when an assert condition fails, or just to document how the website looks on different screens, you can take screenshots from within the tests.

You can save these screenshots to the machine that runs your automated tests. If you are on a CI/CD setup, make sure you transfer these screenshots before you wind down the machine.

Here is how you take a screenshot and save it to your machine in Java:

```java
// Import the relevant packages

// ... your test code

// Take a screenshot and save it to /localtion/to/screenshot.png
driver = (RemoteWebDriver) new Augmenter().augment(driver);
File srcFile = (File) ((TakesScreenshot) driver).getScreenshotAs(OutputType.FILE);
FileUtils.copyFile(srcFile, new File("/location/to/screenshot.png"));

// ... your test code

```
