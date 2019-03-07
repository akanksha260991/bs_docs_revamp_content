# Change screen resolution

You can change screen resolution by using the `resolution` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`resolution`| Set the resolution of your VM before beginning your test |A string. Default is `1024x768`<br/><br/>**Supported resolutions:**<br/>**Windows (XP,7):** `800x600`, `1024x768`, `1280x800`, `1280x1024`, `1366x768`, `1440x900`, `1680x1050`, `1600x1200`, `1920x1200`, `1920x1080`, `2048x1536`<br/><br/>**Windows (8, 8.1, 10):** `1024x768`, `1280x800`, `1280x1024`, `1366x768`, `1440x900`, `1680x1050`, `1600x1200`, `1920x1200`, `1920x1080`, `2048x1536`<br/><br/>**OSX:** `1024x768`, `1280x960`, `1280x1024`, `1600x1200`, `1920x1080`|

For example, if you want to set the resolution to be `1920x1080`, this is what you do in Java:

```java
// Change resolution to 1920x1080

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("resolution", "1920x1080");
```
