# Simulate GPS location

GPS Geolocation testing allows you to simulate the location of the device to a specific GPS location in order to test the location based scenarios for your app. To simulate the GPS location you will need to pass the latitude and longitude of the location you want to set the device for, using gpsLocation parameter in your test.


| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`gpsLocation`|Optional|Required if you want to simulate the location of the device to a particular GPS location. Acceptable range for latitude is -90 to +90 and for longitude is -180 to +180. Example: \"gpsLocation\": \"40.730610,-73.935242\"|
