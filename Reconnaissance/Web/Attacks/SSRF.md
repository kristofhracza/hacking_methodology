# Server-Side Request Forgery (SSRF)

**Server-Side Request Forgery** is a web security vulnerability that allows an attacker to cause the server-side application to make requests to an unintended location


# Example 1

```php
<?php
$new_page = $_GET["page"];
header("Location: $new_page");
?>
```

Here one can just supply any URL as the `page` parameter: `http://remote.com?page=http://10.10.10.10`

It will result in a redirect to the given URL.



# Example 2 (LFI)

```php
<?php
$url = $_GET["url"];
$content = file_get_contents($url);
echo $content;
?>
```

This is a normal LFI, though it can still be classified as SSRF.

One can just use a file as the `url` parameter and read it: `http://remote.com?url=/etc/passwd`



# References
- [https://book.hacktricks.wiki/en/pentesting-web/ssrf-server-side-request-forgery/index.html](https://book.hacktricks.wiki/en/pentesting-web/ssrf-server-side-request-forgery/index.html)
- [https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Request%20Forgery)
- [https://www.invicti.com/blog/web-security/ssrf-vulnerabilities-caused-by-sni-proxy-misconfigurations/](https://www.invicti.com/blog/web-security/ssrf-vulnerabilities-caused-by-sni-proxy-misconfigurations/)
- [https://github.com/swisskyrepo/SSRFmap](https://github.com/swisskyrepo/SSRFmap) -- *Automatic SSRF fuzzer and exploitation tool*