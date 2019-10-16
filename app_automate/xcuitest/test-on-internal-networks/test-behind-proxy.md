## Test websites in environments behind one (or more) proxies
If you have correctly set up Local Testing but are still unable to load your local apps on BrowserStack, then you are probably behind a proxy.

You can use Local Testing to test websites hosted behind proxies, on our remote browsers and devices. The setup will vary based on the proxy implementation in your network. Refer to this article to find proxy settings on your computer or contact your network/IT team.

Currently, we only support Local Testing through the following proxies:

1. Proxy with no authentication or HTTP Basic Authentication.
2. MITM proxy with no authentication or HTTP Basic Authentication.
3. PAC (Proxy Auto-Configuration) with no authentication.

**Note:** For Local Testing to work correctly, bypass traffic for bs-local.com in your proxy server.
