# Set Debuuging Options

You can organize your Appium builds and tests on BrowserStack that will allow you to access the sessions at a later stage using these identifiers. The capabilities available are:

***

1. Define project for your build
2. Define build for your tests
3. Define name for your test run

***


### project
The project can be specified using the capability named project, that allows the user to specify a name for logical group of buils. By default, the project name is Untitled Project. The example of setting this capability:


> "project": "loginformproject"



--


### build
The build can be specified using the capability named build, that allows the user to specify a name for logical group of tests. By default, the build name is Untitled Project. The example of setting this capability:
Allows the user to specify a name for a logical group of tests.

> "build": "build 4.5"


--


### name
The name can be specified using the capability name, that allows the user to specify an identifier for the test run. The example of setting this capability:

> "name": "logintest"

