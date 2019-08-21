# CircleCI

Circleci uses .circleci/config.yml for configuration, and it can be used to integrate your tests with BrowserStack. There are some variables you need to set: grid config, username, access key etc. For more detailed information on setting environment variables in circleci, please refer to their documentation.


### Integrate with App Automate
Here we are providing sample configuration for circleci's config file, by which you can build your app, upload it to app automate and run tests on it. Add following to "steps" in circleci's config.Circleci uses .circleci/config.yml for configuration, and it can be used to integrate your tests with BrowserStack. There are some variables you need to set: grid config, username, access key etc. For more detailed information on setting environment variables in circleci, please refer to their documentation.

### Generate apk
- run: ./gradlew assembleDebug

```bash
# Upload app to BrowserStack and set app url in an environment variable.
# Here replace app-debug.apk with name of your apk.
- run:
    name : App upload and Set app id in environment variable.
    command : |
      APP_UPLOAD_RESPONSE=$(curl -u "USERNAME:ACCESS_KEY" -X POST https://api-cloud.browserstack.com/app-automate/upload -F "file=@app/build/outputs/apk/debug/app-debug.apk")
      APP_ID=$(echo $APP_UPLOAD_RESPONSE | jq -r ".app_url")
      if [ $APP_ID != null ]; then
        echo "Apk uploaded to BrowserStack with app id : ",$APP_ID;
        echo "export BROWSERSTACK_APP_ID=$APP_ID" >> $BASH_ENV;
        source $BASH_ENV;
        echo "Setting value of BROWSERSTACK_APP_ID in environment variables to  ",$APP_ID;
      else
        UPLOAD_ERROR_MESSAGE=$(echo $APP_UPLOAD_RESPONSE | jq -r ".error")
        echo "App upload failed, reason : ",$UPLOAD_ERROR_MESSAGE
        exit 1;
      fi

# run tests!
- run: ./gradlew test
```


Similarly one can build an ios app, upload it and then run tests on it.

Here if app upload is successful then app id of that app will be stored in an environment variable, "BROWSERSTACK_APP_ID" and it can be accessed in your tests in the following way:
```java
String app = System.getenv("BROWSERSTACK_APP_ID"); // Get app id from environment variable
capabilities.setCapability("app", app); // Add app id to driver capability
```

### Running Local Tests
If you are testing in your development or staging environment, you can configure circleci to automatically download the latest version of the BrowserStack Local binary and instantiate the binary before your test starts. In addition to the code below, you will also have to enable local in your test script by setting the capability browserstack.local to true.

Add the following run step in your config file:

### Download and run BrowserStack local
```java
- run:
    name : Download BrowserStack Local binary and start it.
    command : |
      # Download the browserstack binary file
      wget "https://www.browserstack.com/browserstack-local/BrowserStackLocal-linux-x64.zip"
      # Unzip it
      unzip BrowserStackLocal-linux-x64.zip
      # Run the file with user's access key
      ./BrowserStackLocal ACCESS_KEY
 ```
 
 For the complete working example of how to configure Circle CI, check out the [.circleci/config.yml file in our repository](https://github.com/browserstack/browserstack-android-sample-app/blob/master/.circleci/config.yml)
