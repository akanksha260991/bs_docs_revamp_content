# Manage Local Binary Instances

### Determining the status of Local binaries via API
Check the status of all Local binaries that are currently running.

```java
https://www.browserstack.com/local/v1/list?auth_token=ACCESS_KEY&last=5&state=running
```

### Disconnect a Local Testing connection via API
Disconnect an active Local Testing connection through the binary command-line interface.

```bash
curl -X DELETE "https://www.browserstack.com/local/v1/QUERTY1?auth_token=ACCESS_KEY
```
Refer to the [Local API for debugging](https://www.browserstack.com/local-testing/api) binaries page for more details.
