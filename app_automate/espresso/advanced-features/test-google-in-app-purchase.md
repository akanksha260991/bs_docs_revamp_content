# Test Google In-app purchase

If the users can make In-app purchases in your Android apps, you can now test end to end Google in-app purchase flow on BrowserStack devices. To test Google in-app functionality you need to login to the Google account on BrowserStack devices. That is made possible using the appStoreConfiguration parameter by passing your play store username and password. Example: { "username" : "play-store-email", "password" : "play-store-password" }.

Don't worry, your Play Store credentials are completely secured as we wipe the devices clean at the end of your session.

| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`appStoreConfiguration`|Optional|Set this parameter if you want to login to your google account on the devices in order to test the functionalities like Google Pay or Google In-app purchase. Example: { "username" : "play-store-email", "password" : "play-store-password" }|
