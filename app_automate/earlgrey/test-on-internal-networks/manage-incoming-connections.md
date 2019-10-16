# Using Local Testing with App Automate

## Test apps that retrieve data from localhost servers

Once you’ve enabled Local Testing, our remote devices will be able to access apps that retrieve data from your localhost servers.

Use your test scripts to access and interact with your apps. Remember to set ``` browserstack.local```  capability to true in your test scripts.

>Note: Due to security restrictions in Safari (on iOS 10 and above), localhost URLs will automatically change to http://bs-local.com. This lets us load your website assets properly and does not affect your test scripts. Remember to configure your local server to serve requests from bs-local.com

## Test apps that retrieve data from private or internal servers
You can securely test mobile apps that retrieve data from private or internal servers using --force-local on our remote mobile devices. This option resolves all requests (on our remote devices) through your local machine.

To enable this option, use one of two ways to establish the Local Testing connection:

#### Via language bindings:

Add the following snippet to your test scripts:

```java
bsLocalArgs.put("forcelocal", "true");
```

#### Via command-line interface:

Use the **--force-local** flag while establishing a Local Testing connection.

| OS X & Linux  | Windows       |
| ------------- |-------------|
| ```./BrowserStackLocal --key <access-key> --force-local```      | ```BrowserStackLocal.exe --key <access-key> --force-local```|

After establishing the Local Testing connection, use your test scripts to access and interact with your internally-hosted website (**for example, staging.example.com**). Remember to set **browserstack.local** capability to true in your test scripts.



## Test apps that retrieve data from servers behind firewall and/or VPNs
With Local Testing, you can test apps that retrieve data/assets from internal servers that are behind firewalls and/or VPNs.

Start by making sure that you can access the app on your machine. Then, set up a Local Testing connection by following the steps listed in [Test apps that retrieve data from private or internal servers](https://www.browserstack.com/local-testing/app-automate#test-apps-hosted-private-internal-servers).


## Connection duration and disconnection

The Local Testing connection is persistent. Your machine and BrowserStack Cloud remain connected unless you explicitly end the connection.

In the following scenarios, ensure that you open and close the connection by following the steps:

#### Language bindings
Write the code to start the local binary before accessing mobile apps that retrieve content from your local or staging servers. Close the connection after test execution is complete.

#### CI server
Write a script that will establish the Local Testing connection before running a test. Close the connection after test execution is complete.

#### Binary
Run the Local binary using command-line interface with appropriate flags before triggering your tests. Close the connection after test execution is complete.
