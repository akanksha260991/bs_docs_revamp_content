# Accept insecure certificates

Whenever you work with self-signed certificates, or some server with a stale / untrusted certificate, most modern browsers display a security warning or invalid certificate errors.

To avoid these warnings and errors while running your automated tests on BrowserStack, use the `acceptSslCerts` capability.

| Capability | Description | Expected values |
| ---------- | ----------- | --------------- |
|`acceptSslCerts`| Accept all SSL certificates? | A boolean. Default is `False`. <br/><br/>`True` if you want to accept all SSL certificates |
