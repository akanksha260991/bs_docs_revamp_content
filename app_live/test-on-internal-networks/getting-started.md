Local Testing
=============

Local Testing allows you to test your private and internal servers, alongside public URLs, using the BrowserStack cloud, which has support for firewalls, proxies and Active Directory.


### Getting Started

Local Testing establishes a secure connection between your machine and BrowserStack servers. Once you set up Local Testing, all URLs work out of the box, including those with HTTPS, multiple domains, as well as those behind a proxy or firewall, and much more.

#### App Live (using Chrome 31+)

If you have already installed the features extension after signing into BrowserStack, Local Testing will be enabled. There is no additional setup required.

Users have the option of ensuring all traffic routes via their local machine exclusively by selecting the Resolve all URLs through my network checkbox. For example, if there is a public URL like "browserstack.com", but you have /etc/hosts entry for "browserstack.com" mapping to localhost or some other IP, this option will drive all 'browserstack.com' requests from remote browser/mobile device via your machine.

**Note:** This extension is available at the [Chrome](https://chrome.google.com/webstore/detail/browserstack-local/mfiddfehmfdojjfdpfngagldgaaafcfo?hl=en) app store.

![Local Testing](https://dgzoq9b5asjg1.cloudfront.net/production/images/layout/local-testing-docs-img-2.jpg)

**Warning:** Enabling this option slows down the websites on the remote browser.

#### App Live (Firefox 57+)

If you have already installed the BrowserstackLocal app after signing into BrowserStack, Local Testing will be enabled. There is no additional setup required. You can verify this by starting a session and looking for Local enabled sign as follows.

![Local Testing](https://d98b8t1nnulk5.cloudfront.net/production/images/static/docs/local_on.jpg)

If not, follow the steps below

1.  Download the appropriate BrowserstackLocal app
    *   [OS X (10.7 and above)](https://www.browserstack.com/BrowserStackLocal.dmg)
    *   [Windows (XP and above)](https://www.browserstack.com/BrowserStackLocal.exe)
2.  After the file download is complete,
    *   **For mac users**: Click on the BrowserstackLocal.dmg and drag it to applications folder. Start the app to enable local testing by clicking on the BrowserstackLocal app in the Applications folder.
    *   **For windows users**: Click on the BrowserstackLocal.exe, install and run it.
3.  Once you start the BrowserstackLocal app successfully, you will see the following app screen. ![Local App](https://dgzoq9b5asjg1.cloudfront.net/production/images/static/docs/local-success.png)
