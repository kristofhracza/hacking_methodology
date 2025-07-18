# Tips

- When initially moving through the site, do it through `Burpsuite` as it will allow you to see all requests and how they are handled.



# Directories

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

#  Sub-Domains and VHOSTs
```bash
# FFUF
ffuf -u <ip> -H "Host: FUZZ.host.local" -w <wordlist> -mc all

# gubster
gobuster dns -d <host> -w <wordlist>
gobuster vhost -u <host> -w <wordlist>

# wfuzz
wfuzz -u <ip> -H "Host: FUZZ.domain.local" -w <wordlist>
```



# Miscallenous

## More Scans

```sh
# Bruteforce internal port (SSRF)
wfuzz -c -z range,1-65535 --hl=2 http://<ip>:<port>/url.php?path=http://localhost:FUZZ

# URL parameter discovery
wfuzz -u https://<link>/<page>/?FUZZ= -w <wordlist> -H "Cookie: PHPSESSID="
```

## Zap
[Zap](https://www.zaproxy.org/) is a web scanner that simulates human movement and tries to discover the target.
It can also detect basic vulnerabilities as well as weak coding practices.

## Nikto
Scan sites for known vulnerabilities, misconfigurations and directories.

```bash
nikto -h <host>
```


## WordPress
WordPress security scanner `wpscan`

```bash
# This is just my preferred scam please refer to the man page
wpscan --url <url> -e ap,at,dbe,u
```


# Ports
```
80 HTTP
443 HTTPS
8000 HTTP Alternative
8080 HTTP Alternative
```

# Related Attacks and Services list
This is for the **Graph View** of Obsidian
- [[CRLF]]
- [[CSRF]]
- [[File Inclusions]]
- [[File Upload]]
- [[IDOR]]
- [[QUIC]]
- [[SSRF]]
- [[WebDav]]
- [[XSS]]
