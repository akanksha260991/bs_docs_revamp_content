# Integration with Microsoft Visual Studio App Center

Use our integration with App Center to seamlessly test your latest app versions on our Real Device Cloud.

## Introduction

Our seamless API integration allows you to view your latest builds distributed via App Center on App Live. After completing a one-time setup, select the build you want to test and instantly start testing.

## Getting Started

**Get API token from App Center**

To get started, you need to get API token from App Center. Follow these steps to generate the API token:

1. Sign in to your App Center account.
2. Visit Account Settings →[API Tokens](https://appcenter.ms/settings/apitokens)
3. Click on "New API token".
4. Name your token(eg: Integrate App Live) and click "Create".
5. Select the 'Access' as 'Read only'.
6. Generate and copy the API token.
![Create-token](https://d2ogrdw2mh0rsl.cloudfront.net/production/images/static/docs/app-center/create-token.png)

**Sync your apps to App Live**

Follow these steps to sync your apps to App Live:

1. Go to [App Live dashboard](https://app-live.browserstack.com/dashboard).
2. Click on "Sync with App Center" to open the configuration modal.<br>
3. In the configuration modal, paste your API token and "Validate".
4. Select the apps you want to test and click on "Sync".
![Sync-apps](https://www.browserstack.com/images/static/docs/app-center/sync-app.png)

**Test your apps**

Once the setup in completed, you can go to App Live dashboard and start testing any of your builds, including the latest ones. Follow these steps to get started:

1. Select "Upload via App Center" option on App Live dashboard.
2. For the build you want to test, click on sync icon to upload to our servers.
3. Select a device and start testing.

