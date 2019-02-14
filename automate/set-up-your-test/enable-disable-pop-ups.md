# Enable and disable pop-ups

You can enable pop-ups in Chrome, IE and Safari as follows:

|Browser|Steps to enable pop-ups|
|---|---|
|Chrome (v43 and newer)|Pop-ups are enabled by default (as the pop-up blocker is disabled by default).<br/><br/>If you enabled the pop-up blocker using the steps in the next section, remove the code that enables the pop-up blocker.|
|Chrome (v42 and older)|To enable pop-ups, you have to disable the pop-up blocker by creating a `chromeOptions` capability, and passing the `--disable-popup-blocking` argument. <br/><br/><pre><code>ChromeOptions options = new ChromeOptions();&#10;options.addArguments("--disable-popup-blocking");&#10;caps.setCapability(ChromeOptions.CAPABILITY, options);</code></pre>|
|Internet Explorer|To enable the popups in IE, use the `browserstack.ie.enablePopups` capability. <br/><br/><pre><code>caps.setCapability("browserstack.ie.enablePopups", "true");</code></pre>|
|Safari|To enable the popups in Safari, use the `browserstack.safari.enablePopups` capability. <br/><br/><pre><code>caps.setCapability("browserstack.safari.enablePopups", "true");</code></pre>|

You can disable pop-ups in Chrome, IE and Safari as follows:

|Browser|Steps to disable pop-ups|
|---|---|
|Chrome (v43 and newer)|To enable the popup blocker, create a `chromeOptions` capability, and pass the `disable-popup-blocking` as `excludeSwitches`. <br/><br/><pre><code>ChromeOptions options = new ChromeOptions();&#10;options.setExperimentalOption("excludeSwitches", "disable-popup-blocking");&#10;caps.setCapability(ChromeOptions.CAPABILITY, options);</code></pre>|
|Chrome (v42 and older)|Pop-ups are disabled by default. <br/><br/> If you enabled them using the steps in the previous section, remove the code that disables the pop-up blocker.|
|Internet Explorer|Pop-ups are disabled by default.<br/><br/>If you enabled them using the steps in the previous section, remove the code that disables the pop-ups|
|Safari|Pop-ups are disabled by default.<br/><br/>If you enabled them using the steps in the previous section, remove the code that disables the pop-ups|
