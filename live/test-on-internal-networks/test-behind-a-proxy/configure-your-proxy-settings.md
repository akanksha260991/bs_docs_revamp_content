#### Behind a proxy

Enable Local Testing and type the address, for example **staging.example.com**, in the remote browser, and browse as you would on your local machine.

##### Using Local Testing Binary

When using the command line to enable Local Testing, setup the connection using the **\--proxy-host** and **\--proxy-port** parameters.  
Example:

```
./BrowserStackLocal  --key iEVwsD2LptX29uSUUzDZ --proxy-host <proxy\_host> --proxy-port <proxy\_port>
```

##### BrowserMob Proxy

When using the command line to enable Local Testing, setup the connection using the **\--local-proxy-host** and **\--local-proxy-port** parameters.  
Example:

```
./BrowserStackLocal --key iEVwsD2LptX29uSUUzDZ --local-proxy-host <browsermob\_proxy\_host> --local-proxy-port <browsermob\_proxy\_port> --local-proxy-user <proxy\_username> --local-proxy-pass <proxy\_password>
```

You can ignore **\--local-proxy-host** and **\--local-proxy-pass** if your proxy does not have authentication. This is valid for local binary versions v5.8 and above. To know your binary version execute **./BrowserStackLocal  --version**
