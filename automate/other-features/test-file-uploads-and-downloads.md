# Test file uploads and downloads
In some scenarios, you would want to test uploading files (an image, or a PDF etc.,) on your website and would want to test the behaviour when the file is successfully uploaded, and when it fails. Similarly, you might want to download a file and check the contents of that file (for example, a dynamically generated CSV or a PDF etc.,).


## Upload files
Uploading files while executing the test is straight forward. Here is an example of uploading an image using a form:

```java
// Set the file detector
driver.setFileDetector(new LocalFileDetector());

// Load your page / form
driver.get("http://example.com/");

// ... Fill the form fields as needed

// Upload the file
driver.findElement(By.id("file_upload")).sendKeys("/path/to/your/file.jpg");

// ... Fill the rest of the form fields as needed
// Submit the form
```

You can write a similar code to upload the file for non-form scenarios.

## Download files
Downloading files in Chrome is straight forward. Just perform a `click` on the link that downloads the file. You can set the Chrome Options to define where the file should be downloaded to, and use that path to read and check the downloaded file.

In Firefox, downloading files while the test is running involves creating a `profile` capability containing all the necessary parameters, and then associate it with the `WebDriver`. Here is an example:

```java
// Create a Firefox profile
FirefoxProfile profile = new FirefoxProfile();
profile.setPreference("browser.download.folderList", 0);
profile.setPreference("browser.download.manager.showWhenStarting", false);
profile.setPreference("browser.download.manager.focusWhenStarting", false);
profile.setPreference("browser.download.useDownloadDir", true);
profile.setPreference("browser.helperApps.alwaysAsk.force", false);
profile.setPreference("browser.download.manager.alertOnEXEOpen", false);
profile.setPreference("browser.download.manager.closeWhenDone", true);
profile.setPreference("browser.download.manager.showAlertOnComplete", false);
profile.setPreference("browser.download.manager.useWindow", false);

// Set the content-type of the file you wish to download
profile.setPreference("browser.helperApps.neverAsk.saveToDisk", "application/octet-stream");

// Use the profile while using BrowserStack's remote browser
DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserName", "firefox");
caps.setCapability(FirefoxDriver.PROFILE, profile);
WebDriver driver = new RemoteWebDriver(new URL(URL), caps);

// Open a page, and click on the download link

// Wait for sometime to let the download complete
// Or you can write some callback / promise
Thread.sleep(50000);
```

If you need to download these downloaded files to your local machine, you can copy these files to your local machine just like you saved screenshots to disk.
