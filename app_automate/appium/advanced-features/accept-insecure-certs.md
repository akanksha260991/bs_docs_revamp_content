# Accept insecure certificates

#### Whenever you work with self-signed certificates in your apps testing, or some server with a stale / untrusted certificate, most modern browsers display a security warning or invalid certificate errors.

To avoid these warnings and errors while running your automated tests, use the `browserstack.acceptInsecureCerts` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|**browserstack.acceptInsecureCerts** | Use this capability to avoid invalid certificate errors while using self-signed certificate to test your app | True/False. Default is `False`. 
