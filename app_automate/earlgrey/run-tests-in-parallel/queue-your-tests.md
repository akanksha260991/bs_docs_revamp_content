# Queue tests

With queuing, you can launch an additional number of parallel tests with different device configurations that will be queued in a sequence. For instance, if you want to run 5 additional tests, apart from your subscribed limit of, say, 2 parallel tests, we will queue the additional 5 tests until one of the 2 initial tests finish, and a slot is available for execution. With queuing, you don't need to worry about managing your test pipeline - we automatically take care of scheduling and execution for you.

With this feature, accounts up to 5 parallel tests can queue 5 tests. Beyond 5 parallel tests, an equivalent number of tests will be queued. For example, if you have 2 parallel tests for your account, you can queue up to 5 more tests. If you have 200 parallel tests, you can queue up to 200 more tests.

> **Note:** The wait limit for the execution of a pending queued job is 15 minutes and will be cancelled if exceeded.

## Scenarios where queuing helps
Queuing is enabled by default by BrowserStack to ensure you don't have to worry about managing the test execution on your end. This helps especially in the following scenarios:

* When you have multiple projects / PRs to test using the same BrowserStack account. If the other project is already running at full capacity, your new tests are better off in a queue than getting discarded altogether.

* When you have multiple users in your team who use the same BrowserStack account for their projects. Rationing of parallel tests across teams can be inefficient - instead queuing can help you manage the test executions in a simpler manner.

* If the number of total test cases is within the queue limit and if you don't want to write some logic to dispatch tests, you can just submit all your tests to BrowserStack, and we will automatically manage the test runs for you.

In a few rare cases, BrowserStack will automatically put your tests in a queue for a short period of time so your other tests can run first. This is usually when the device & OS configurations you want to run your test on has a long start-time delay (either because of peak usage or booting up time). This makes way for the next test which can be quickly run - thus reducing the overall time taken to run your tests. The previously queued test will be automatically dequeued and run whenever a slot becomes available.
