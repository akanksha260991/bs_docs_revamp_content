# Simulate network conditions

In many cases, you would want to test how your websites looks and performs on browsers with different networks (2G, 3G, LTE etc.,) that have varied upload & download speeds & latencies.

To simulate these network conditions, you can use one of our presets, or define your own custom network conditions using the `browserstack.networkProfile` and `browserstack.customNetwork` capabilities. You can also change the network conditions mid-way during the test run, just like in real world where the end-user's network varies.

## Specifying the network profile to start the tests with
You can use the `browserstack.networkProfile` capability to set the network profile the test should start with. By default, each device will have unhindered WiFi connection with Internet connectivity.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`browserstack.networkProfile`| The network profile to start the test with | A string. <br/><br/>`2g-gprs-good`, <br/> `4g-lte-advanced-lossy`<br/><br/>See the [list of all pre-defined network profiles](#complete-list-of-network-profiles)|

If you have unique network conditions to test in, you can use the `browserstack.customNetwork` to specify the connection speed, latencies and the packet loss %.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`browserstack.customNetwork`| The custom network condition to start the test with | A string. <br/><br/>`1000,1000,100,1`<br/><br/>Specify the desired download speed, upload speed, latency in milliseconds and packet loss percentage |

## Changing the network profile while the tests are running
You can use the following REST API to switch network profiles in the middle of the test run.

| # | Value |
| --- | --- |
| HTTP Method | PUT |
| API Endpoint | `https://api.browserstack.com/automate/sessions/<sessionid>/update_network.json` |
| Content type | application/json |
| Authorization | Basic authorization with your username and accesskey |
| JSON to send | `{"networkProfile":"4g-lte-good"}` or `{"customNetwork":"1000,1000,100,1"}` |

Read more about [getting the session ID of the running test](/docs/selenium/java-testng/get-session-id).

## Complete list of network profiles

| Profile Name | Download Speed (Kbps) | Upload Speed (Kbps) | Latency (ms) | Packet Loss (%) |
| --- | --- | --- | --- | --- |
| 2g-gprs-good | 50 | 30 | 500 | 1 |
| 2g-gprs-lossy | 30 | 20 | 650 | 2 |
| edge-good | 250 | 150 | 300 | 0 |
| edge-lossy | 150 | 100 | 500 | 1 |
| 3g-umts-good | 400 | 100 | 100 | 0 |
| 3g-umts-lossy | 200 | 50 | 200 | 1 |
| 3.5g-hspa-good | 1800 | 400 | 100 | 0 |
| 3.5g-hspa-lossy | 900 | 200 | 190 | 1 |
| 3.5g-hspa-plus-good | 7000 | 1500 | 100 | 0 |
| 3.5g-hspa-plus-lossy | 2000 | 600 | 130 | 1 |
| 4g-lte-good | 18000 | 9000 | 100 | 0 |
| 4g-lte-high-latency | 18000 | 9000 | 3000 | 0 |
| 4g-lte-lossy | 7000 | 3000 | 120 | 1 |
| 4g-lte-advanced-good | 25000 | 18000 | 80 | 0 |
| 4g-lte-advanced-lossy | 15000 | 10000 | 70 | 1 |
| no-network <br/>**_Android only_** | 0 | 0 | 0 | 0 |
| airplane-mode <br/>**_Android only_** | 0 | 0 | 0 | 0 |
