# Test apps that retrieve data from servers behind proxies

Test apps that retrieve data from servers behind proxies
If you have correctly set up Local Testing but are still unable to load your app's assets on remote mobile devices, then you are probably behind a proxy.

You can test apps serving content from private or internal servers that are behind proxies—on our remote mobile devices. Refer to this article to find proxy settings on your computer or contact your network/IT team.

Currently, we only support Local Testing through the following proxies:

1. Proxy with no authentication or HTTP Basic Authentication.
2. MITM proxy with no authentication or HTTP Basic Authentication.
3. PAC (Proxy Auto-Configuration) with no authentication.


> **Note:** For Local Testing to work correctly, bypass traffic for bs-local.com in your proxy server.

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


