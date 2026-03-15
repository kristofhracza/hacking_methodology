# Methodology / Checklist
If it is a real-life pen test, refer to and start with [[Web - IRL]] for low hanging fruits. Otherwise:
- [ ] Walked the site with `Burp`
- [ ] Checked directories and files
- [ ] Viewed `Console` and `Debugger`/`Source` in browser along with page source
	- [ ] Looked for error messages, identified source code?
- [ ] Mapped out tech-stack
	- [ ] Looked for exploits and CVEs
	- [ ] If there is a templating engine, tried SSTI
- [ ] If relevant, tried exploiting session tokens/cookies
- [ ] Tried boiler plate attacks (open redirect, HTTP request smuggling etc)
- [ ] HTTP method testing to uncover unexpected functionalities
- [ ] Checked for underlying API
- [ ] **Manually** examined functions
	- [ ] Forced errors
	- [ ] Exploited or attempted to exploit
		- [ ] Specific?
		- [ ] Business Logic?
- [ ] Identified 401/403... error pages (bypass)
- [ ] Examined `Burp` history and Target listing

**If real life assessment, don't forget to use rate limiting**


# Enumeration
## Directories

```bash
# Feroxbuster
feroxbuster -u <host>
feroxbuster -u <host> -x html,php

# FFUF
ffuf -u <host>/FUZZ -w <wordlist>
ffuf -u <host>/FUZZ -w <wordlist> -e .txt,.php,.html

# gobuster
gobuster dir -u <IP> -w <wordlist>
gobuster dir -u <IP> -w <wordlist> -x txt,php,html
```
##  Sub-Domains and VHOSTs
```bash
# FFUF
ffuf -u <ip> -H "Host: FUZZ.host.local" -w <wordlist> -mc all

# gubster
gobuster dns -d <host> -w <wordlist>
gobuster vhost -u <host> -w <wordlist>

# wfuzz
wfuzz -u <ip> -H "Host: FUZZ.domain.local" -w <wordlist>
```
## CMS

```bash
wpscan --url <url> -e ap,at,dbe,u # -- my preffered choice
wpscan --force update -e --url <URL>

cmsmap [-f W] -F -d <URL>

joomscan --ec -u <URL>
joomlavs.rb #https://github.com/rastating/joomlavs
```

## Miscellaneous

```sh
# Bruteforce internal port (SSRF)
wfuzz -c -z range,1-65535 --hl=2 http://<ip>:<port>/url.php?path=http://localhost:FUZZ

# URL parameter discovery
wfuzz -u https://<link>/<page>/?FUZZ= -w <wordlist> -H "Cookie: PHPSESSID="
```



# Ports
```
80 HTTP
443 HTTPS
8000 HTTP Alternative
8080 HTTP Alternative
```


# Attack Vectors
- [[CRLF - Carriage Return Line Feed]]
- [[CSRF - Cross Site Request Forgery]]
- [[File Inclusions]]
- [[File Upload]]
- [[IDOR]]
- [[Log Poisoning]]
- [[Object Injection]]
- [[Open Redirect]]
- [[QUIC]]
- [[SSRF - Server-Side Request Forgery]]
-  [[Server Side Template Injection - SSTI]]
- [[WebDav]]
- [[XSS - Cross-Site Scripting]]
## Misc
- [[Web - IRL| Real Life Web Flags]]


# References
- [https://hacktricks.wiki/en/network-services-pentesting/pentesting-web/index.html](https://hacktricks.wiki/en/network-services-pentesting/pentesting-web/index.html)
- [https://hacktricks.wiki/en/pentesting-web/web-vulnerabilities-methodology.html](https://hacktricks.wiki/en/pentesting-web/web-vulnerabilities-methodology.html)