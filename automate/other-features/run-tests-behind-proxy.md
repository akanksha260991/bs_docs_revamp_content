# Run tests from behind a proxy

If your machine is behind a proxy, you won't be able to connect to BrowserStack's remote browsers directly. You need to set your proxy settings, so that the connections go through normally. Here is how you do it in Java:

```java
//For HTTP
System.getProperties().put("http.proxyHost", "<HOST>");
System.getProperties().put("http.proxyPort", "<PORT>");
System.getProperties().put("http.proxyUser", "<USER>");
System.getProperties().put("http.proxyPassword", "<PASSWORD>");

//For HTTPS
System.getProperties().put("https.proxyHost", "<HOST>");
System.getProperties().put("https.proxyPort", "<PORT>");
System.getProperties().put("https.proxyUser", "<USER>");
System.getProperties().put("https.proxyPassword", "<PASSWORD>");
```

Remember that `System.getProperties().put` will not change your system properties. It only modifies the `Property` object within the scope of the running program. Once the program is executed, these properties will no longer be accessible. Hence you need to set these every time your test suite is run.
