## Set Localization 

To test the localized version of your app, you can set:

a. Timezone 
b. Devices language
c. Locale


### Timezone
In order to configure tests to run on a custom time zone (supported only for Android), use **browserstack.timezone** capability

> "browserstack.timezone" : "UTC"

> Note: UTC, New_York (for America/New_York), Los_Angeles (for America/Los_Angeles), La_Rioja (for America/Argentina/La_Rioja). You can view the complete [list of timezones](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)

--

### Language
In order to set the language for iOS (XCUITest driver only) and Android, use **language** capability

> "language" : "fr"

--

### Locale
Locale to set for iOS (XCUITest driver only) and Android 


> "locale" : "fr_CA"

> Note: fr_CA format for iOS. CA format (country name abbreviation) for Android



