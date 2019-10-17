# Set Localization Options

You can change the language, locale and timezone of the app if you want to perform localization testing of your app. Use below parameters while executing your test to set the specific language and locale values.

| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`language`|Optional|Set this parameter if you want to change the language to test localization of your App. Example: \"language\": \"fr\"|
|`locale`|Optional|Set this parameter if you want to change the locale to test localization of your App. Example: \"locale\": \"fr\"|
|`timezone`| Optional | Set this parameter if you want to configure tests to run on a custom time zone. Example: UTC, New_York (for America/New_York), Los_Angeles (for America/Los_Angeles), La_Rioja (for America/Argentina/La_Rioja). You can view the [complete list of timezones on Wikipedia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |




## Command

```bash
curl -X POST "https://api-cloud.browserstack.com/app-automate/xcuitest/build" -d "{\"devices\": [\"iPhone 8 Plus-11\"], \"app\": \"bs://<hashed appid>\", \"language\" : \"fr\", \"deviceLogs\" : \"true\", \"testSuite\": \"bs://<hashed testID>\"}" -H "Content-Type: application/json" -u "USERNAME:ACCESS-KEY"
```
