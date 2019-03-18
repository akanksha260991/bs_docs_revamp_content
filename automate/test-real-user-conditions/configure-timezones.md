# Configure time zones

In case your website changes depending on your end-users's timezone, you might want to test that functionality by specifying a time zone you want to test in. You can configure your tests to run on a custom time zone by using the `browserstack.timezone` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`browserstack.timezone`| The time zone you want to run the test in | A string. <br/><br/>`New_York` for America/New_York, <br/> `La_Rioja` for America/Argentina/La_Rioja<br/><br/>See the [complete list of time zones on Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List)|

Here is the sample code that sets the time zone to America/New_York in Java:

```java
// Testing with time zone set to New York

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserstack.timezone", "New_York");
// ... other capabilities
```
