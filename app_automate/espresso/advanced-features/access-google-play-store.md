# Access Google Play Store

If you would like to test your production Android app, you can access the Google Play Store on the BrowserStack devices, search for the app using the app bundle id and download it on the device. In order to login to the Play Store, use appStoreConfiguration parameter and pass your play store username and password. Example: { "username" : "play-store-email", "password" : "play-store-password" }. This will login the Android device with the Play Store credentials you passed.

Don't worry, your Play Store credentials are completely secured as we wipe the devices clean at the end of your session.


| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`appStoreConfiguration`|Optional|Set this parameter if you want to login to your google account on the devices in order to test the functionalities like Google Pay or Google In-app purchase. Example: { "username" : "play-store-email", "password" : "play-store-password" }|
