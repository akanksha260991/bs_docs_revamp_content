# Allow all cookies in Safari

You can allow all cookies in Safari using the `browserstack.safari.allowAllCookies` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`browserstack.safari.allowAllCookies`|Enable all cookies in Safari?|A string. Default is `false`. <br/><br/>`true` if you want to enable all cookies in Safari|

For example, this is how you allow all cookies in Safari:

```java
// Allowing all cookies in Safari

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("Browser.safari.allowAllCookies", "true");
```
