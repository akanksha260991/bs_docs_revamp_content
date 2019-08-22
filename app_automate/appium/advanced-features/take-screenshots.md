# Capture Screenshots

#### Take a screenshot of the current viewport

In order to file a bug when an assert condition fails, or just to document how the app looks on different screens, you can take screenshots from within the tests.

You can save these screenshots to the machine that runs your automated tests. If you are on a CI/CD setup, make sure you transfer these screenshots before you wind down the machine.

Here is how you take a screenshot and save it to your machine in Java:

```java
// Import the relevant packages

// ... your test code

// Take a screenshot and save it to /location/to/screenshot.png
File scrFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
FileUtils.copyFile(srcFile, new File("/location/to/screenshot.png"));


// ... your test code

```


> Note: Some platforms may have settings that prevent screenshots from being taken, for security reason. One such feature is the [Android FLAG_SECURE](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_SECURE) layout parameter

