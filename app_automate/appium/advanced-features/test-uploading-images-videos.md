# Test using uploaded Images and Video

### Use your uploaded images or videos in the tests and execute them on BrowserStack's Real Device Cloud

Upload your custom media on BrowserStack servers using [REST API](https://browserstack.com/app-automate/rest-api). Make use of the **browserstack.uploadMedia** capability:


```bash
"browserstack.uploadMedia" : ["media://hashedid", "media://hashedid"]
```


> **Note:** 

* Only 5 files are allowed per test
* Supported format for images are JPG, JPEG, PNG, GIF, BMP
* Max file size allowed for images is 10MB
* Supported format for videos are .mp4, .mov and .3gp
* Max file size allowed for videos is 50MB
