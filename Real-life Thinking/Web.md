
# HTTP Headers
- Is the app missing the following headers, if so, flag it:
	- [ ] **Strict-Transport-Security (HSTS)**: Tells the browser to only use HTTPS.
		- `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload`
		- if `max-age` is more than **31536000** (1 year), flag it.
	- [ ] **X-Frame-Options**: Stops the app from being loaded into an `i-frame` element.
	- [ ] **X-Permitted-Cross-Domain-Policies** : Tells old versions of Adobe clients where to load cross-domain policy file from.
		-  `X-Permitted-Cross-Domain-Policies: none`
	- [ ] **Cache-Control**: Used to tell the app not to store HTTPS response in the cache.
		-  `Cache-control: no-set` or `Pragma: no-cache`
	- [ ] **Content-Security-Policy**: Helps to filter XSS or clickjacking attacks.
		-  `Content-Security-Policy: nosniff
	- [ ] **Access-Control-Allow-Origin (CORS)**: This header indicates whether the response it is related to can be shared with requesting code from the given origin
		- [ ] If set, don't use wildcard, otherwise, remove.
	
- Does the app have the following in place, if so, flag and disable it:
	- [ ] **X-Powered-By**: Tells attackers the vendor of the site.
	- [ ] **Sever**: Shows the server name.
	- [ ] **X-AspNet-Version**: Provides information about the .NET version of the site.

# SSL/TLS
*Test for these with `sslscan` or `testssl`*
- [ ] Deprecated TLS versions supported: Do they support TLS version 1.2 or before?
- [ ] Do they use weak cipher suites?

# Cookie Attributes
*Are these set? If not, flag it.*
- [ ] `Secure`
- [ ] `HTTPOnly`

# References
- [https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.htms](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Headers_Cheat_Sheet.html)