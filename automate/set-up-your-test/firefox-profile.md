# Working with Firefox Profile

To change the preferences within Firefox, instantiate a new `FirefoxProfile` object and update the preferences of the profile. Check out more preferences at the [MozillaZine Knowledge Base](http://kb.mozillazine.org/About:config_entries).

In this example, a custom Firefox Profile is used to disable Flash in Firefox.

```java
FirefoxProfile profile = new FirefoxProfile();
profile.setPreference("plugin.state.flash", 0);
caps.setCapability(FirefoxDriver.PROFILE, profile);
```
