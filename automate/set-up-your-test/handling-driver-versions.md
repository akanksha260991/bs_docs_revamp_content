# Handling driver versions

You can handle driver versions and architecture for Chrome, IE and Firefox.

## Chrome drivers

You can use the `browserstack.chrome.driver` capability to specify the Chrome driver's version you want to use.

|Capability|Description|Expected Values|
|---|---|---|
|`browserstack.chrome.driver`|The version of Chrome driver you want to use|A string. By default, BrowserStack automatically selects the driver version based on the browser version provided.<br/><br/>**Supported values:** `20`, `2.3`, `2.6`, `2.9`, `2.10`, `2.11`, `2.12`, `2.15`, `2.16`, `2.18`, `2.19`, `2.20`, `2.21`, `2.22`, `2.23`, `2.24`, `2.25`, `2.26`, `2.27`, `2.28`, `2.29`, `2.30`, `2.31`, `2.32`, `2.33`, `2.34`, `2.35`, `2.36`, `2.37`, `2.38`, `2.39`, `2.40`, `2.41`, `2.42`, `2.43`, `2.44`, `2.45`<br/><br/>To ensure you use the right version of Chrome with the right version of the Chrome driver, please use our [Capability Builder](https://www.browserstack.com/automate/capabilities#capabilities-generator), which automatically shows you the right combinations of Chrome and Chrome driver.|
