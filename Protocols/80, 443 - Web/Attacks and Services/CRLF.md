# Carriage Return Line Feed (CRLF) Injection Attack

In the HTTP protocol, the **CR-LF** sequence is always used to terminate a line.

A CRLF Injection attack occurs when a user manages to submit a CRLF into an application. This is most commonly done by modifying an HTTP parameter or URL.



# Exploitation

## HTTP Response Splitting

Manipulating HTTP headers by injecting CRLF characters.
  
One could craft a malicious URL containing CRLF characters to add extra headers or modify existing ones. Consider the following vulnerable code:

```php
<?php
$new_url = $_GET["url"];
header("Location: " . $new_url);
?>
```

An attacker could exploit this code by appending CRLF characters and injecting a custom header like:

```
http://remote.com/index.php?url=http://remote.com/%0D%0AContent-Length:%200%0D%0A%0D%0AHTTP/1.1%20200%20OK%0D%0A%0D%0AeSomeContent
```

This vulnerability could lead to other attacks such as; XSS, Cache Poisoning, Page Hijacking

  
## Log Forging
The bad actor injects CRLF characters into application logs, creating fake log entries or modifying existing ones.

  
## Bypassing sanity checks
Some applications perform poorly written checks on the user input.
However, most of these checks are not checking for CRLF sequences.

For example:

There's a web app that checks for the parameter **role**. If the parameter is present it will assign a session variable called **role**. With this, a user is able to access the dashboard of the given role.

```
# Original Request
GET /get_profile.php?role=admin
```

Here the check would be performed which would result in an error.

```
# CRLF Request
GET /get_profile.php?role%0d%0a=admin
```

However, with the new request the **role** parameter has passed the check.


# References
- [https://book.hacktricks.wiki/en/pentesting-web/crlf-0d-0a.html](https://book.hacktricks.wiki/en/pentesting-web/crlf-0d-0a.html)
- [https://www.invicti.com/blog/web-security/crlf-http-header/](https://www.invicti.com/blog/web-security/crlf-http-header/)
- [https://portswigger.net/research/making-http-header-injection-critical-via-response-queue-poisoning](https://portswigger.net/research/making-http-header-injection-critical-via-response-queue-poisoning)