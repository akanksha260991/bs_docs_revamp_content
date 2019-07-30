
Integration URL
===============

You can create URLs, with testing options as parameters, to instantly start a browser on BrowserStack. You can integrate these URLs into your application, bookmark, and share them with others.

### How to integrate

#### Integration parameters

Parameters & Values

**os**  
OS you want to integrate.

Windows, OS X, Android, iOS, or Winphone.  
**Default**: Windows

**os\_version**  
OS version you want to integrate.

Windows: XP, 7, 8 and 8.1.  
OS X: Lion, Snow Leopard, Mountain Lion, Mavericks, Yosemite.  
Default: XP for Windows, Snow Leopard for OS X

**browser** (desktop only)  
Browser you want to integrate.

Firefox, Safari, IE, Chrome, Opera.  
**Default:** Firefox

**browser\_version** (desktop only)  
Browser version you want to integrate.

View list of browser versions [list of browser versions](https://www.browserstack.com/list-of-browsers-and-platforms?product=live) to update this field.  
**Default:** 10.0

**device** (mobile only)  
Mobile device you want to integrate.

View [list of browser versions](https://www.browserstack.com/list-of-browsers-and-platforms?product=live#ios) to update this field.

**resolution**  
Resolution you want to integrate.

**responsive-mode:** Uses entire screen of your browser and accesses any resolution by just resizing your browser.

**Windows (XP, 7):** 800x600, 1024x768, 1280x800, 1280x1024, 1366x768, 1440x900, 1680x1050, 1600x1200, 1920x1200, 1920x1080, 2048x1536

**Windows (8,8.1,10):** 1024x768, 1280x800, 1280x1024, 1366x768, 1440x900, 1680x1050, 1600x1200, 1920x1200, 1920x1080, 2048x1536

**OS X:** 1024x768, 1280x960, 1280x1024, 1600x1200, 1920x1080

**Default:** responsive-mode

**scale\_to\_fit**  
Enable or disable scale to fit mode.

true or false  
**Default:** true

**speed**  
Toggle between fast speed or high quality.

1 for fast speed, 2 for high quality.

**Default**: 1

**start**  
Enable or disable auto start mode.

true or false  
**Default:** false

**src**  
Unique tracking parameter.

Please use a special key for traffic from your source so that we can track it accurately.

**url**  
Specify URL to test.

URLs can be of 3 types:  
**Public:** e.g. google.com  
**Local folders:** e.g. filename that needs to be tested e.g. test.html  
**Local servers:** e.g. http://localhost:3000/sign\_up

**Local Testing using Extensions**

**local**  
Enable or disable Local Testing.  
  
**force-local**  
Define whether all URL's, private or public, should be resolved through your network.

Mandatorily pass local=true to enable Local Testing.  
**Default**: false  
  
true or false  
**Default:** false

**Local Testing using Binaries** (works only for browsers which don't support Local Testing extensions)

**local**  
Enable or disable Local Testing.

**use\_cmd\_local**  
Useful for browsers not supporting [Local Testing browser extensions](https://www.browserstack.com/local-testing#extensions).

  
  
**folder**  
**Note:** Local folder testing with browser extensions via Integrations API is not supported.

Mandatorily pass local=true to enable Local Testing.  
**Default:** false

Pass use\_cmd\_local=true if you specifically want to use the binaries for Local Testing.  
**Default**: false  
_**Note:** Local Testing using binaries should already be running in order to use this feature_.

If you want to do local folder testing, then specify the absolute path of your directory in following format:  
**Mac:** /Users/..  
**Windows:** C:\\Users\\Public\\..>

#### Examples

**To test Windows 7 / IE 8 / responsive-mode:**

> https://live.browserstack.com/dashboard#os=Windows&os\_version=7&browser=IE&browser\_version=8.0&scale\_to\_fit=true&url=www.google.com&resolution=responsive-mode&speed=1&start=true

**To test Windows 7 / IE 8 / 1024x768:**

> https://live.browserstack.com/dashboard#os=Windows&os\_version=7&browser=IE&browser\_version=8.0&scale\_to\_fit=true&url=www.google.com&resolution=1024x768&speed=1&start=true

**To test Mac Lion / FF 12.0 / 1920x1080:**

> https://live.browserstack.com/dashboard#os=OS+X&os\_version=lion&browser=Firefox&browser\_version=12.0&scale\_to\_fit=true&url=www.google.com&resolution=1920x1080&speed=1&start=true


**To test local servers on Mac Lion / FF 12.0 / 1920x1080 using multiple Local Testing connections:**

>https://live.browserstack.com/dashboard#os=OS+X&os\_version=lion&browser=Firefox&browser\_version=12.0&scale\_to\_fit=true&url=localhost:3000&resolution=1920x1080&speed=1&start=true&local=true&host\_ports=localhost,3000,0,localhost,443,1

**To test local folders (e.g. /Users/BrowserStack/test.html) on Mac Lion / Chrome 24.0 / 1920x1080 using Local Testing binaries:**

*   [OS X & Linux](# "OS X & Linux")
  > ./BrowserStackLocal -f EUpiWB2sGpuWTmp4RHDC /Users
*   [Windows](# "Windows")
   > BrowserStackLocal.exe -f EUpiWB2sGpuWTmp4RHDC /Users

https://live.browserstack.com/dashboard#os=OS+X&os\_version=lion&browser=Chrome&browser\_version=24.0&scale\_to\_fit=true&resolution=1920x1080&speed=1&start=true&use\_cmd\_local=true&local=true&folder=/Users&url=/BrowserStack/test.html

To instantly start a browser on BrowserStack, you can use URLs which have testing options as parameters. URLs can be integrated directly into applications, bookmarked, or shared with others.
