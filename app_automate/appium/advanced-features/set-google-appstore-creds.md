# Set AppStore Configuartion (Android)

#### To test functionalities like Google Pay or Google In-app purchase, you need to login to your google account on the devices

On App Automate, you can make use of the `browserstack.appStoreConfiguration` capability for enabling this:

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`browserstack.appStoreConfiguration`| Login to your google account on the devices to test the functionalities like Google Pay etc. | {"username" : "play-store-email", "password" : "play-store-password"} |
