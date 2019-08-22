# Execute ADB Commands
Though BrowserStack does not open the ADB shell, we support most of the ADB commands either in the form of a capability of an API.
ADB provides users a CLI to communicate with Android devices, like installing apps, granting permissions, copying files, profiling for performance etc., Learn how to achieve what the most commonly used ADB commands do on BrowserStack.

### Managing Applications
| What needs to be done?	| Using ADB	| Using BrowserStack |
| ---------- | ----------- | --------------- |
| Install packages	| adb install [options] package_name <br><br> adb install-multiple [options] packages <br><br> adb shell pm install [options] path | Installing packages on devices you want to run your tests on is simple. <br><br> First, you must upload your apps and obtain the app's hashed ID. Refer to upload app section to see how this can be done. <br><br> Second, mention the hashed IDs of the apps you want to install in the desired capabilities while running a new test session with BrowserStack. The app you want to test will be your main app, and the apps your main app depends on will be the otherApps. |
| Uninstall packages |	adb uninstall package	| You don't have to explicitly uninstall an app after you're done with your tests. We automatically uninstall your apps, delete the data that was added / modified during your tests and clean up the device before running another test session on that device. <br><br> If you want to uninstall the package, and reinstall it during the same session, we recommend you to reset the app instead. |
| Reset the app or clear package data |	adb shell pm clear package	| You can use [Appium's reset package](https://appium.io/docs/en/commands/device/app/reset-app/) functionality to clear all the data associated with the app. |

### File Transfers

| What needs to be done?	| Using ADB	| Using BrowserStack |
| ---------- | ----------- | --------------- |
| Copy a file or directory and its sub-directories to the device.|	adb push local remote	|By default, BrowserStack provides a set of images & videos you can use to test whether file uploads work fine in your app. But, if you choose to use your own images & videos, you can upload your custom media and use them in your tests.<br><br>First, upload your custom media to BrowserStack and obtain the media's hashed ID. Refer to the [upload media section](https://www.browserstack.com/docs/app-automate/espresso/upload-media-files) to see how this can be done.<br><br>Second, mention the hashed IDs of the media you want to access in the desired capabilities while running a new test session with BrowserStack. These should be mentioned as an array in the browserstack.uploadMedia capability.<br><br>You can also use [Appium's push file](https://appium.io/docs/en/commands/device/files/push-file/) functionality, but you can only push files into the /data/tmp/ folder, and can pull the file from that location using [Appium's pull](https://appium.io/docs/en/commands/device/files/pull-file/) functionality. |
|Copy a file or directory and its sub-directories from the device.|	adb pull remote local	|At the moment, we don't support pulling a file from the device to your local system.<br><br>If you want to take a screenshot, store it locally on the device, and retrieve it using pull command, you can use [Appium's screenshot](https://appium.io/docs/en/commands/session/screenshot/) functionality to get the screenshot in base64 or PNG format that you can write to a file locally on your machine.<br><br>You can also use BrowserStack's Screenshots functionality to generate screenshots. Just pass the browserstack.debug capability in your tests' desired capabilities, and BrowserStack will automatically generate screenshots at various steps in your test so that you can consume them / debug using them later.<br><br>You can also use [Appium's pull file](https://appium.io/docs/en/commands/device/files/pull-file/) functionality, but you can only pull files from the /data/tmp folder. You can also push files to the same location using [Appium's push](https://appium.io/docs/en/commands/device/files/push-file/) functionality.

Granting Permissions to Apps
What needs to be done?	Using ADB	Using BrowserStack
Grant a permission to an app	adb shell pm grant package_name permission
You can auto-grant permissions passing the autoGrantPermissions capability while starting the test.

You can also handle with the permission alerts, and click the allow button during the test.

Revoke a permission from an app	adb shell pm revoke package_name permission
You cannot do this on BrowserStack at the moment.

If you need to perform your tests with limited permissions, we recommend you to run a new test session and allow / disallow the required permissions during the test by handling the permission alerts.

Debugging Options
What needs to be done?	Using ADB	Using BrowserStack
Print log data to the screen	adb logcat [options]
You can access these logs in the Device Logs section of the test session page in your App Automate dashboard.

You can also retrieve these logs via the REST API.

Working with Android Activity & Animations
What needs to be done?	Using ADB	Using BrowserStack
Start an Activity specified by intent	adb shell am start [options] intent	You can use Appium's start activity functionality to start an Android activity by providing package name and activity name.
Force stop everything associated with package	adb shell am force-stop package
You can use Appium's close app functionality to force stop and close your application.

Remember that you can only force stop your main application (specified by the app capability ), but not the applications your main application depends on (the ones specified in otherApps capability).

Disable system animations to speed up your tests and improve stability	adb shell am instrument --no-window-animation [options]
You can now use Appium's capability disableWindowAnimation to disable system's window animations.

Remember that you need to also set the browserstack.appium_version capability to 1.9.1 or later because this capability is not available in the older versions of Appium.

Screen Capture & Recording
What needs to be done?	Using ADB	Using BrowserStack
Capture a screenshot and save it to file	adb shell screencap filename
You use BrowserStack's Screenshots functionality to generate screenshots. Just pass the browserstack.debug capability in your tests' desired capabilities, and BrowserStack will automatically generate screenshots at various steps in your test so that you can consume them / debug using them later.

You can use Appium's screenshot functionality to get the screenshot in base64 or PNG format that you can write to a file locally on your machine.

Record display of devices	adb shell screenrecord [options] filename	You can use browserstack.video capability in your tests' desired capabilities if you want to enable video recording during your test.
App Profiling
What needs to be done?	Using ADB	Using BrowserStack
Understand the battery consumption, memory & CPU utilization & network performance	adb dumpsys [options]	You can get this information from your App Automate dashboard on the test session page.
Note: App profiling is only available for Android.
