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

## Test apps that retrieve data from servers behind proxies
If you have correctly set up Local Testing but are still unable to load your app's assets on remote mobile devices, then you are probably behind a proxy.

You can test apps serving content from private or internal servers that are behind proxies—on our remote mobile devices. Refer to this article to find proxy settings on your computer or contact your network/IT team.

Currently, we only support Local Testing through the following proxies:

* Proxy with no authentication or HTTP Basic Authentication.
* MITM proxy with no authentication or HTTP Basic Authentication.
* PAC (Proxy Auto-Configuration) with no authentication.


>Note: For Local Testing to work correctly, bypass traffic for bs-local.com in your proxy server.

Once you’ve identified the proxy type and settings, follow the steps below to set up Local Testing.

### Proxy
Proxies are commonly set up for office networks, remote servers, and/or your local machine. Contact your network/IT team to obtain the ‘Proxy Host’ and ‘Proxy Port’ to setup Local Testing for this implementation.

Once you obtain the Host and Port, you can establish a Local Testing connection using one of two ways:

#### Via language bindings:

Add the following snippet to your test scripts:

```java
bsLocalArgs.put("proxyHost", "127.0.0.1");
bsLocalArgs.put("proxyPort", "8000");
```


If your proxy requires authentication, use the following code snippet:
```java
bsLocalArgs.put("proxyHost", "127.0.0.1");
bsLocalArgs.put("proxyPort", "8000");
bsLocalArgs.put("proxyUser", "user");
bsLocalArgs.put("proxyPass", "password");
```

#### Via command-line interface:
Use the **--proxy-host** and **--proxy-port** flags while establishing a Local Testing connection:

Run the binary using the following command:

| OS X & Linux  | Windows       |
| ------------- |-------------|
| ```./BrowserStackLocal --key <access-key> --proxy-host <proxy_host> --proxy-port <proxy_port>```      | ```BrowserStackLocal.exe --key <access-key> --proxy-host <proxy_host> --proxy-port <proxy_port>```|


If your proxy requires authentication, add --proxy-user and --proxy-pass to the above command.

| OS X & Linux  | Windows       |
| ------------- |-------------|
| ```./BrowserStackLocal --key <access-key> --proxy-host <proxy_host> --proxy-port <proxy_port> --proxy-user --proxy-pass```      | ```BrowserStackLocal.exe --key <access-key> --proxy-host <proxy_host> --proxy-port <proxy_port> --proxy-user --proxy-pass```|

To resolve all requests on our remote browsers and mobile devices through your proxy, add --force-proxy and --force-local flags to the command. Without these flags, Local binary tries to connect directly for better performance.


### MITM Proxy
If your proxy type is MITM (like BrowserMob), you will need the ‘Proxy Host’ and ‘Proxy Port’. Once you have the Host and Port, you can establish a Local Testing connection using one of two ways:

Via language bindings:

Add the following snippet to your test scripts:

```java
bsLocalArgs.put("localProxyHost", "127.0.0.1");
bsLocalArgs.put("localProxyPort", "8000");
```

If your proxy requires authentication, use the following code snippet:

```java
bsLocalArgs.put("localProxyHost", "127.0.0.1");
bsLocalArgs.put("localProxyPort", "8000");
bsLocalArgs.put("-localProxyUser", "user");
bsLocalArgs.put("-localProxyPass", "password");
```

#### Via command-line interface:

Use the following command to establish the Local Testing connection:

Run the binary using the following command:

| OS X & Linux  | Windows       |
| ------------- |-------------|
| ```./BrowserStackLocal --key Gj9ztcdyupnWkwjmuTis --local-proxy-host <proxy_host> --local-proxy-port <proxy_port> --local-proxy-user <proxy_user> --local-proxy-pass <proxy_pass>```      | ```BrowserStackLocal.exe --key Gj9ztcdyupnWkwjmuTis --local-proxy-host <proxy_host> --local-proxy-port <proxy_port> --local-proxy-user <proxy_user> --local-proxy-pass <proxy_pass>```|

If your local proxy requires authentication, add **--local-proxy-user** and **--local-proxy-pass** the command, as shown below:

To resolve all requests on our remote browsers and mobile devices through your local proxy, add **--force-proxy** and **--force-local** flags to the command. Without these flags, Local binary tries to connect directly for enhanced performance.

### PAC (Proxy auto-config)
PAC file (Proxy auto-configuration file) contains a Javascript function that determines whether a request should be sent via the proxy server.

Once you have the absolute path of the PAC file on your machine, you can establish a local testing connection using one of two ways:

#### Via language bindings:

Add the following snippet to your test scripts:

```java
bsLocalArgs.put("-pac-file", "<pac_file_abs_path>");
```

#### Via command-line interface:

Establish the Local Testing connection using --pac-file flag, along with the PAC file’s absolute path.
Run the binary using the following command:

| OS X & Linux  | Windows       |
| ------------- |-------------|
| ```./BrowserStackLocal --key <access-key> --pac-file <pac_file_abs_path>```      | ```BrowserStackLocal.exe --key <access-key> --pac-file <pac_file_abs_path>```|


> Note: We do not support authentication with PAC file at the moment.

To resolve all requests with Local Testing through your PAC proxy, add **--force-proxy** and **--force-local** flags to the command.

Without these flags, Local binary tries to connect directly for enhanced performance.

#### Sample PAC file

The following function checks to see whether the hostname is localhost, and if so, whether the connection is direct. If the hostname is not localhost, the connection is via proxy.

```
function FindProxyForURL(url, host) {
if (isPlainHostName(host))
  return "DIRECT";
else
  return "PROXY proxy:80";
}
```

If you don't understand the proxy script or are not authorized to make any changes to it, contact your IT/Network team.


## Test apps that retrieve data from servers behind firewall and/or VPNs
With Local Testing, you can test apps that retrieve data/assets from internal servers that are behind firewalls and/or VPNs.

Start by making sure that you can access the app on your machine. Then, set up a Local Testing connection by following the steps listed in [Test apps that retrieve data from private or internal servers](https://www.browserstack.com/local-testing/app-automate#test-apps-hosted-private-internal-servers).



## Multiple Local Testing connections
You can test multiple forks and builds at the same time by setting up separate Local Testing connections for each. To do so, establish local testing connections using one of two ways:

#### Via language bindings:

Add the following snippet to your test scripts:

```java
bsLocalArgs.put("localIdentifier", "randomstring");
```

#### Via command-line interface

Establish each Local Testing connection using the **--local-identifier** flag, along with a unique connection name (for instance, **Test 123**).

>Note: Not using the **--local-identifier** flag (for each Local Testing connection) will cut off the existing connection and create a new one.

| OS X & Linux  | Windows       |
| ------------- |-------------|
| ```./BrowserStackLocal --key Gj9ztcdyupnWkwjmuTis --local-identifier Test123```      | ```BrowserStackLocal.exe --key Gj9ztcdyupnWkwjmuTis --local-identifier Test123```|

After establishing Local Testing connections, configure the test script to run through a specific connection. To do so, set the browserstack.localIdentifier capability in your test scripts—and add the unique connection name as parameter.

```java
caps.setCapability("browserstack.local", "true");
caps.setCapability("browserstack.localIdentifier", "Test123");
```

#### Managing multiple Local Testing connections
You can use the [Local Testing API](https://www.browserstack.com/local-testing/api) to check the status of (or disconnect) active binaries. However, your connections will not show up in the API response unless you establish them using '--enable-logging-for-api' flag, as follows:

```java
./BrowserStackLocal --key ACCESS_KEY --enable-logging-for-api <other_params>
```

#### Determining the status of Local binaries via API
Check the status of all Local binaries that are currently running.

```java
https://www.browserstack.com/local/v1/list?auth_token=ACCESS_KEY&last=5&state=running
```

#### Disconnect a Local Testing connection via API
Disconnect an active Local Testing connection through the binary command-line interface.

```bash
curl -X DELETE "https://www.browserstack.com/local/v1/QUERTY1?auth_token=ACCESS_KEY
```
Refer to the [Local API for debugging](https://www.browserstack.com/local-testing/api) binaries page for more details.


## Connection duration and disconnection

The Local Testing connection is persistent. Your machine and BrowserStack Cloud remain connected unless you explicitly end the connection.

In the following scenarios, ensure that you open and close the connection by following the steps:

#### Language bindings
Write the code to start the local binary before accessing mobile apps that retrieve content from your local or staging servers. Close the connection after test execution is complete.

#### CI server
Write a script that will establish the Local Testing connection before running a test. Close the connection after test execution is complete.

#### Binary
Run the Local binary using command-line interface with appropriate flags before triggering your tests. Close the connection after test execution is complete.
