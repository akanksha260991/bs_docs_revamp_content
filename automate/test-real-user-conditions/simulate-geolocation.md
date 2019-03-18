# Simulate geolocation

If your website serves different content based on the country your end-users live in, you will want to test that functionality. You can use the `browserstack.geoLocation` capability to test your websites across different countries using [BrowserStack's IP Geolocation](https://www.browserstack.com/ip-geolocation) feature.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`browserstack.geoLocation`| The country you want your test to detect | A string. An ISO 2 country code<br/><br/>`FR` for France, <br/> `CN` for China<br/><br/>See the [list of 45+ countries we support](https://www.browserstack.com/ip-geolocation#ip-geolocation-countries-supported)|

Here is the sample code that sets the geolocation to France:

```java
// Testing with the IP geolocation set to France

DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("browserstack.geoLocation", "FR");
// ... other capabilities
```

Note: This feature only sets the IP to the selected geolocation, and not guaranteed to set the appropriate timezone. If you want to use the timezone in that country, you will have to [configure a custom timezone](/docs/selenium/java-testng/configure-timezones).
