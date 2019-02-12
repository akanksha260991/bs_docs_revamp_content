# Working with chromeOptions

A `ChromeDriver` session can further be customized and configured using `ChromeOptions`. BrowserStack supports the full complement of `ChromeOptions`.

In this example, `ChromeOptions` is used to disable Flash in Chrome.

```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--disable-plugins");
caps.setCapability(ChromeOptions.CAPABILITY, options);
```

You can read all about ChromeDriver and ChromeOptions [here](http://chromedriver.chromium.org/capabilities). A complete set of options / command line switches that you can use with `ChromeOptions` can be found [here](https://peter.sh/experiments/chromium-command-line-switches/)
