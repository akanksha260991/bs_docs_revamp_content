# Organize tests

You can organize your automated tests by projects and builds by using the `project` and `build` capabilities.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
| `project`  | Name of your project | A string. <br/><br/> Note that only letters (`A-Z`, `a-z`), digits (`0-9`), periods (`.`), colons (`:`) and underscores (`_`) are allowed. Any other characters are ignored. |
| `build`  | Build version / number | A string. <br/><br/> Note that only letters (`A-Z`, `a-z`), digits (`0-9`), periods (`.`), colons (`:`) and underscores (`_`) are allowed. Any other characters are ignored. |

For example, if you are working on your new marketing website, and want to run your test suite for the Alpha 0.1.7 build, you can pass the following capabilities:

```java
DesiredCapabilities caps = new DesiredCapabilities();
caps.setCapability("project", "Marketing Website v2");
caps.setCapability("build", "alpha_0.1.7");
```

Remember to update the build version / number the next time you run the test again. If you are running your tests on a different project, remember to update the project name too.
