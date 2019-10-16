# Local Testing with App Automate
This guide shows you how to set up and use Local Testing with BrowserStack App Automate.

## Introduction
Enabling Local Testing with App Automate is a simple two-step process:

a. Establishing a Local Testing connection.
b. Configuring test scripts so they run through the Local Testing connection.
<br>
This page will guide you through enabling Local Testing for App Automate sessions, and then using it to test apps that retrieve data from servers on your local machine, CI/CD machines/nodes, and other private network configurations.



## Enabling Local Testing with App Automate

### Establishing a Local Testing connection
You can establish Local Testing connection in one of two ways: via language bindings, or via the command-line interface.

#### Via language bindings:

Within your test scripts, you can add a snippet that will automatically start and end the Local Testing connection.

To do this, you’ll need bindings for your programming language. Select the language and follow the steps below:

##### Installation:

```java
<dependency>
    <groupId>com.browserstack</groupId>
    <artifactId>browserstack-local-java</artifactId>
    <version>1.0.3</version>
</dependency>
```
##### Example:
```
import com.browserstack.local.Local;

# creates an instance of Local
Local bsLocal = new Local();

# replace <browserstack-accesskey> with your key. You can also set an environment variable - "BROWSERSTACK_ACCESS_KEY".
HashMap<String, String> bsLocalArgs = new HashMap<String, String>();
bsLocalArgs.put("key", "<browserstack-accesskey>");

# starts the Local instance with the required arguments
bsLocal.start(bsLocalArgs);

# check if BrowserStack local instance is running
System.out.println(bsLocal.isRunning());

#stop the Local instance
bsLocal.stop();
```
Refer to this [GitHub Repository](https://github.com/browserstack/browserstack-local-java) for more details

#### Via command-line interface:

You can also start a Local Testing connection through your command-line interface by following the steps below:

1. Download the appropriate binary for your system:

  * [OS X (10.7 and above)](https://www.browserstack.com/browserstack-local/BrowserStackLocal-darwin-x64.zip)
  * [Linux 32-bit](https://www.browserstack.com/browserstack-local/BrowserStackLocal-linux-ia32.zip)
  * [Linux 64-bit](https://www.browserstack.com/browserstack-local/BrowserStackLocal-linux-x64.zip)
  * [Windows (XP and above)](https://www.browserstack.com/browserstack-local/BrowserStackLocal-win32.zip)

**Note:** Note: The download links are secure. The binaries are digitally signed, identifying the publisher as 'BrowserStack Ltd'.


2. Unzip the binary to a folder/directory on your machine.
3. Open your command-line interface and navigate to the folder containing the Local binary.
4. Run the binary using the following command

| OS X & Linux  | Windows       |
| ------------- |-------------|
| ```./BrowserStackLocal --key <access-key>```      | ```BrowserStackLocal.exe --key <access-key>```

### Configuring tests to run with Local Testing connection

After establishing the Local Testing connection, set **browserstack.local** capability to **true** by adding the following snippet to your test scripts:

```
caps.setCapability("browserstack.local", "true");
```

**Note:** The binary connection needs to be established **before** creating a test session, and disconnected only **after** test execution is complete.

The Local Testing setup will differ slightly based on your requirements (for example, proxy settings, restricting connections to certain domains, etc.). Refer to our [complete list of flags](https://www.browserstack.com/local-testing/binary-params) to set up Local Testing for different environments/network configurations.
