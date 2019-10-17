# Simulate Network Conditions

You can now simulate different network conditions to test the performance of your apps in those networks. When you run your tests with the default network settings, each device will have unhindered WiFi connection with Internet connectivity. With Network Simulation, you can change the WiFi connection to simulate the speeds of network profiles like 2G or 3G that controls bandwidth, latency and packet loss.


| Parameter | Required / Optional | Description |
| ---------- | ----------- | --------------- |
|`networkProfile`|Optional|Required if you want to simulate different network conditions from the list of existing network profiles. Example: \"networkProfile\" : \"2g-gprs-good\". Complete list of network profiles can be found below.|
|`customNetwork`|Optional|Required if you want to simulate the custom network conditions. Accepted values: download speed (kbps), upload speed (kbps), latency (ms), packet loss (%). Example: \"customNetwork\" : \"1000,1000,100,1\"|

### Complete list of network profiles
| Profile Name	| Bandwidth in/out (Kbps)	| Latency (ms)	| Packet Loss (%) |
| ---------- | ----------- | --------------- | --------------- |
| 2g-gprs-good	| 50/30	| 500	| 1 |
| 2g-gprs-lossy	| 30/20	| 650	| 2 |
| edge-good	| 250/150	| 300	| 0 |
| edge-lossy	| 150/100	| 500	| 1 |
| 3g-umts-good	| 400/100	| 100	| 0 |
| 3g-umts-lossy	| 200/50	| 200	| 1 |
| 3.5g-hspa-good	| 1800/400	| 100	| 0 |
| 3.5g-hspa-lossy	| 900/200	| 190	| 1 |
| 3.5g-hspa-plus-good	| 7000/1500	| 100	| 0 |
| 3.5g-hspa-plus-lossy	| 2000/600	| 130	| 1 |
| 4g-lte-good	| 18000/9000	| 100	| 0 |
| 4g-lte-high-latency	| 18000/9000	| 3000	| 0 |
| 4g-lte-lossy	| 7000/3000	| 120	| 1 |
| 4g-lte-advanced-good	| 25000/18000	| 80	| 0 |
| 4g-lte-advanced-lossy	| 15000/10000	| 70	| 1 |
| no-network (Android only)	| 0	| 0	| 0 |
| airplane-mode (Android only)	| 0	| 0 | 0 |
| reset	| Set Network settings to Browserstack's default configuration. |  | | |
