#### PAC file support

PAC file stands for Proxy Auto-Configuration file. It is a javascript function that determines whether a request should be sent via the web proxy server or not. You can use PAC files on BrowserStack using the **\--pac-file** parameter

```
./BrowserStackLocal --key iEVwsD2LptX29uSUUzDZ --pac-file <pac\_file\_abs\_path>
```

##### Sample PAC file

The following function checks to see whether the hostname is localhost, and if so, whether the connection is direct. If the hostname is not localhost, the connection is via proxy.

```
function FindProxyForURL(url, host) {
  if (isPlainHostName(host))
 	  return "DIRECT";
  else
 	  return "PROXY proxy:80";
}             

```
