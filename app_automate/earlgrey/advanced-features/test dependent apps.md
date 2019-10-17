# Test Dependent Apps

Install and test multiple apps on the device to test the flows dependent on other apps. You can install dependent apps using the otherApps parameter. Example: \"otherApps\" : [\"bs://<hashed-id>\"]. Maximum 3 app ids allowed. This parameter has to be used along with app parameter.

| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`otherApps`|Optional|Set this capability if you want to install apps in addition to the main app. Example: \"otherApps\" : [\"bs://\"]. Maximum 3 app ids allowed. This parameter has to be used along with app parameter.|
