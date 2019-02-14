# Disable Flash

You can disable Flash in Chrome, Firefox and IE as follows:

| Browser | Steps to disable Flash |
| --- | --- |
|Chrome| To disable Flash in Chrome, create a `chromeOptions` capability, and pass the `--disable-plugins` argument. Note that using this argument will turn off all the plugins in the browser. <br/><br/> <pre lang=java><code>ChromeOptions options = new ChromeOptions(); &#10;options.addArguments("--disable-plugins");&#10;caps.setCapability(ChromeOptions.CAPABILITY, options);</code></pre>|
|Firefox| To disable Flash in Firefox, set the `flash` capability to `0`. <br/><br/> <pre lang=java><code>FirefoxProfile profile = new FirefoxProfile();&#10;profile.setPreference("plugin.state.flash", 0);&#10;caps.setCapability(FirefoxDriver.PROFILE, profile);</code></pre>|
|Internet Explorer| To disable Flash in Internet explorer, pass the `browserstack.ie.noFlash` capability in your tests.<br/><br/> <pre lang=java><code>FirefoxProfile profile = new FirefoxProfile();&#10;profile.setPreference("plugin.state.flash", 0);&#10;caps.setCapability(FirefoxDriver.PROFILE, profile);</code></pre>|
