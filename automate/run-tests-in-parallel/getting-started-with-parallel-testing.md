# Get started with Parallel Testing

On BrowserStack, you can run multiple Selenium Webdriver tests at the same time across various browser, device and OS combinations. This is **Parallel Testing**. This helps in reducing the time it takes for your test suite to run.

For example, if your test suite takes **30 minutes** to execute sequentially, running it with 10 parallel tests might cut down the test execution time to be as low as **3 minutes** (of course, this is based on the assumption that all your test cases take more or less the same time).

## Running tests in parallel
You can parallelize your test runs by using multiple-threads or multiple-processes on your machines. Most modern testing frameworks (and test runners) have the built-in ability to run parallel tests.

Each of these parallel tests will create a new session on BrowserStack and gets executed independently.

Note that you need to buy sufficient parallel sessions to be able to run parallel tests. If you trigger more tests than what you subscribed for, they will either be [queued](/docs/selenium/java-testng/queue-tests) or discarded.

## Parallelizing your existing test suite
In most cases, your test suite will run just fine when parallel tests are triggered.

Usually, modifications will be needed to your test suite if you:
* **Care for the order of test execution**: Depending on the test execution time, the time the test gets dispatched to BrowserStack, and the device availability on BrowserStack, tests might be completed out-of-order. For example, if you trigger tests 1, 2, 3, 4 at the same time, the test results might be in 4, 2, 1, 3 order. If you care for the order of test execution, it is best to group them together and instruct the test framework (or the test runner) to dispatch the tests sequentially.

* **Wrote your code assuming only one test at a time**: If your code assumes that only one test would run (for example, if you are using a resource lock for the entire duration of a single test), you will have to change that logic to facilitate access to multiple tests.

* **Are using [Local Testing](/docs/selenium/java-testng/getting-started-with-local-testing)**: To be able to serve the increased number of incoming requests, you can either use the `--parallel-runs` flag so the binary will automatically create multiple processes to serve the requests, or use `--local-identifier` and spin multiple binary tunnels separately. Note that if you choose to go the local identifier route, your tests should be able to choose the tunnel that needs to be used.

If you need any further help, feel free to contact our Support team who are always ready to help you out! You can also use our [Parallel requirement calculator](/automate/parallel-calculator) to figure out the optimal number of parallel tests you should buy & run so you can finish all of your tests faster.
