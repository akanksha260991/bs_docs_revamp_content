# Using Basic HTTP Authentication

If your website is protected by basic auth, the login prompt comes in way before you can run any automated tests. You can deal with this alert (or prompt) by using WebDriver's `Alert` class and sending keys for your username and password.

You can avoid this login prompt altogether by URL encoding your username and password.

## URL Encoding
This is achieved by prepending `username:password@` to the hostname in the URL. Note that if you have `@` or `:` in your password or username you need to URL encode them too.

For example, the following would access the page index.html at the web site www.example.com with the secure HTTPS protocol and provide the username Aladdin and the password OpenSesame credentials via basic authorization:

```bash
https://Aladdin:OpenSesame@www.example.com/index.html
```

So, the following code snippet will open the index.html page on www.example.com in the BrowserStack remote browsers without prompting for login:

```java
// Start a remote browser with the desired capabilities
WebDriver driver = new RemoteWebDriver(new URL(URL), caps);

// ... Get the URL
driver.get("https://Aladdin:OpenSesame@www.example.com/index.html")
```

**Note:** Some browsers (some versions of Chrome & IE) don't support URL encoding anymore as per [RFC 3986](https://tools.ietf.org/html/rfc3986#section-3.2.1). We recommended you not to use Basic Auth in your test environments where possible so you can run your tests in a variety of browsers. You might instead want to setup a local server and use our [local testing features](/docs/selenium/java-testng/getting-started-with-local-testing) to test your websites.
