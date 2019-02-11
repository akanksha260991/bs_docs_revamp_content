# Organize tests

You can organize your automated tests by projects and builds by using the `project`, `build`, and `name` capabilities.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
| `name`  | Name for your session / test case | A string. <br/><br/> Note that only letters (`A-Z`, `a-z`), digits (`0-9`), periods (`.`), colons (`:`) and underscores (`_`) are allowed. Any other characters are ignored. |
| `build`  | Build version / number | A string. <br/><br/> Note that only letters (`A-Z`, `a-z`), digits (`0-9`), periods (`.`), colons (`:`) and underscores (`_`) are allowed. Any other characters are ignored. |
| `project`  | Name of your project | A string. <br/><br/> Note that only letters (`A-Z`, `a-z`), digits (`0-9`), periods (`.`), colons (`:`) and underscores (`_`) are allowed. Any other characters are ignored. |

For example, if you are working on your new marketing website, and want to run your test suite for the Alpha 0.1.7 build, you can pass the following capabilities:

```java
DesiredCapabilities caps = new DesiredCapabilities();

// Testing the home page
caps.setCapability("project", "Marketing Website v2");
caps.setCapability("build", "alpha_0.1.7");
caps.setCapability("name", "Home page must have a title");

// Testing the admin page
caps.setCapability("project", "Marketing Website v2");
caps.setCapability("build", "alpha_0.1.7");
caps.setCapability("name", "Admin page access must be denied");
```

You can use the `name` capability to give your session a name (usually describing the test case) so it is easy for you to debug later.

Remember to update the build version / number the next time you run the test again. If you are running your tests on a different project, remember to update the project name too.
