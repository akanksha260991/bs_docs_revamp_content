# Multiple Local Testing connections
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

