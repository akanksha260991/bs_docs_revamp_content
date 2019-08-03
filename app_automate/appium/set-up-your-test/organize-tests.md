# Organize Your Tests

You can organize your Appium builds and tests on BrowserStack that will allow you to access the sessions at a later stage using these identifiers. The options available are:

***

1. Project Name for your build
2. Build Name for your tests
3. Name for your test run

***


### Project Name
Use the capability **project**, to specify a name for a logical group of builds. The default value is Untitled Project. The example of setting this capability:


> "project": "loginformproject"


--


### Build Name
Use the capability **build**, to specify a name for a logical group of tests. The default value is Untitled Build. The example of setting this capability:

> "build" : "build 4.5"


--


### Test Name
Use the capability **name**, to specify an identifier for your test run. The example of setting this capability:

> "name" : "logintest"

