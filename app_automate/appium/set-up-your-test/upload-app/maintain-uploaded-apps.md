# Maintain Uploaded Apps

You can maintain your uploaded apps ( uploaded from local machine or using a public url ). The options available are:

***

1. Define Custom ID for your app
2. Get recent uploads
3. Get recent uploads for custom id
4. Get recent uploads for entire group

***


### Define Custom ID for your App

If you would like to set a constant value in your 'app' capability instead updating your test code to change the App URL everytime you upload an app, define a Custom Id for your apps. Use the same Custom Id for every build you upload. Custom Id is optional.

```
curl -u "<username>:<accesskey>" \
-X POST "https://api-cloud.browserstack.com/app-automate/upload" \
-F "file=@/path/to/app/file/Application-debug.apk" \
-F "data={\"custom_id\": \"MyApp\"}"
```

Sample response of the API call

```
{"custom_id":"MyApp", "app_url":"bs://<hashed appid>", "shareable_id":"USERNAME/MyApp"}
```
> Note: custom_id is optional. You can upload multiple builds under same custom_id. Use custom_id in 'app' capability for Appium to always pick the last uploaded build.

--

### Get Recent Uploads

An API to retrieve details about your recent App uploads. The response will not include the deleted apps
```
curl -u "<username>:<accesskey>" -X GET https://api-cloud.browserstack.com/app-automate/recent_apps
```

--

### Get Recent Uploads for Custom ID

An API to retrieve details about your recent App uploads under a specific custom id. The response will not include the deleted apps
```
curl -u "<username>:<accesskey>" -X GET "https://api-cloud.browserstack.com/app-automate/recent_apps/<custom_id>"
```

--

### Get Recent Uploads for Entire Group

An API to retrieve details about your recent App uploads for the entire group. The response will not include the deleted apps
```
curl -u "<username>:<accesskey>" -X GET "https://api-cloud.browserstack.com/app-automate/recent_group_apps"
```

--

### Delete App

An API to DELETE an uploaded App
```
curl -u "akanksha50:PFphTtU8jcpyB4hifUMB" -X DELETE https://api-cloud.browserstack.com/app-automate/app/delete/<AppId>
```




