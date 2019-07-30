# HockeyApp Integration

Use our HockeyApp integration to seamlessly test any of your HockeyApp builds on our Real Device Cloud.

## Introduction

Our seamless API integration allows you to view your latest builds distributed via HockeyApp on App Live. Once setup is done, select the build you want to test and instantly start testing.

## Getting Started

**Get HockeyApp API token**

To setup integration, you need to get API token from HockeyApp. Follow these steps to generate the API token: 
1. Sign in to HockeyApp with an account having Owner/Developer/Member role. 
2. Visit Account Settings → [API Tokens](https://rink.hockeyapp.net/manage/auth\_tokens) 
3. From App dropdown, select "All apps" or the specific app you want to test. 
4. From Rights dropdown, select "Full Access".
5. Name your token(optional) and click "Create". 
![Create HockeyApp API token](https://d98b8t1nnulk5.cloudfront.net/production/images/static/docs/hockeyapp/create-token.png)

**Sync your apps to App Live**

Follow these steps to sync your apps to App Live:
1. Go to [App Live dashboard](https://app-live.browserstack.com/dashboard). 
2. Click on "Sync with HockeyApp" to open the configuration modal. 
![Sync with HockeyApp](https://d3but80xmlhqzj.cloudfront.net/production/images/static/docs/hockeyapp/sync-app.png)
3. In the configuration modal, paste your API token and "Validate". 
4. Select the apps you want to test and click on "Sync". 


**Test your apps**

Once the setup in completed, you can go to App Live dashboard and start testing any of your builds, including the latest ones. Follow these steps to get started: 
1. Select "Upload via HockeyApp" option on App Live dashboard. 
2. For the build you want to test, click on sync icon to upload to our servers. 
3. Select a device and start testing.
