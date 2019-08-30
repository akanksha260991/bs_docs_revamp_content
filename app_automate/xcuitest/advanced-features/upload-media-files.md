# Upload Media Files

You can upload your custom media files like images and videos to the BrowserStack servers for you to be able to use in your test. You can upload your custom media on BrowserStack servers using [REST API](https://www.browserstack.com/app-automate/rest-api?framework=xcuitest#media-upload).

Use the hashed url returned as a result of the upload to set uploadMedia parameter in your test run.

Note:
-Only 5 files are allowed per test
-Supported format for images are JPG, JPEG, PNG, GIF, BMP
-Max file size allowed for images is 10MB
-Supported format for videos are .mp4, .mov and .3gp
-Max file size allowed for videos is 50MB

| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`uploadMedia`|Optional|Set this parameter if you want to use your uploaded images or videos in the test. Upload your custom media on BrowserStack servers using REST API. Use the hashed url returned as a result of the upload in this capability. Example: ["media://hashedid", "media://hashedid"]|
