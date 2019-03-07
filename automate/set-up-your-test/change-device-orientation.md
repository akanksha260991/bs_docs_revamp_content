# Change device orientation

You can change the device orientation by using the `deviceOrientation` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`deviceOrientation`|Set the screen orientation of the mobile device|A string. Default is `portrait`<br/><br/>`portrait` if you want the device to be in portrait mode. `landscape` otherwise.|

For example, if you want to set the device orientation to be `landscape`, this is what you do in Java:

```java
// Change device orientation to landscape

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("deviceOrientation", "landscape");
```
