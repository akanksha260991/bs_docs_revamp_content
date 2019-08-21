# Simulate Network Conditions

#### Simulate different network conditions to test the performance of your apps in those networks

When you run your tests with the default network settings, each device will have unhindered WiFi connection with Internet connectivity. With Network Simulation, you can change the WiFi connection to simulate the speeds of network profiles like 2G or 3G that controls bandwidth, latency and packet loss

Below are the network profiles you can use in the **browserstack.networkProfile** (Android) capability in order to simulate the different network speeds:

> "browserstack.networkProfile" : "2g-gprs-good"


| **Profile Name**              | **Bandwidth in/out (Kbps)**  |  **Latency (ms)**    |  **Packet Loss (%)**  |
| ------------------------------|:----------------------------:| :-------------------:|:---------------------:|
| 2g-gprs-good                  | 	50/30                      |      500             |  1                    |
| 2g-gprs-lossy			            | 	30/20                      |      650             |  2                    |
| edge-good		                  | 	250/150                    |      300             |  0                    |
| 2gedge-lossy					        | 	150/100                    |      500             |  1                    |
| 3g-umts-good					        | 	400/100                    |      100             |  0                    |
| 3g-umts-lossy					        | 	200/50                     |      200             |  1                    |
| 3.5g-hspa-good			          | 	1800/400                   |      100             |  0                    |
| 3.5g-hspa-lossy			          | 	900/200                    |      190             |  1                    |
| 3.5g-hspa-plus-good		        | 	7000/1500                  |      100             |  0                    |
| 3.5g-hspa-plus-lossy	        | 	2000/600                   |      130             |  1                    |
| 4g-lte-good				            | 	18000/9000                 |      100             |  0                    |
| 4g-lte-high-latency		        | 	18000/9000                 |      3000            |  0                    |
| 4g-lte-lossy				          |  7000/3000                   |      120             |  1                    |
| 4g-lte-advanced-good	        | 	25000/18000                |      80              |  0                    |
| 4g-lte-advanced-lossy	        | 	15000/10000                |      70              |  1                    |
| no-network (Android only)     | 	0                          |      0               |  0                    |
| airplane-mode (Android only)  | 	0                          |      0               |  0                    |
| Reset                         | 	Set Network settings to Browserstack's default configuration              |

--

#### Switch network profiles in the middle of the test


Use the below REST API If you want to switch between network profiles in the middle of your test run.



```
curl -u "USERNAME:ACCESS-KEY" -H "Content-Type: application/json" -d "{\"networkProfile\":\"4g-lte-good\"}" -X PUT "https://api-cloud.browserstack.com/app-automate/sessions/<sessionid>/update_network.json"
```


```
curl -u "USERNAME:ACCESS-KEY" -H "Content-Type: application/json" -d "{\"customNetwork\":\"1000,1000,100,1\"}" -X PUT "https://api-cloud.browserstack.com/app-automate/sessions/<sessionid>/update_network.json"
```

Get session id using driver.session_id once the driver is created

--

#### Simulate Custom Network Condition

Required if you want to simulate the custom network condition

> "browserstack.customNetwork" : "1000, 1000, 100, 1"

> Note: "1000, 1000, 100, 1" refers to download speed (kbps), upload speed (kbps), latency (ms), packet loss (%) in that order









