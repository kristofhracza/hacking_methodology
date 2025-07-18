# Basic Information
**Web Distributed Authoring and Versioning (WebDAV)** is an extension of the HTTP protocol that allows users to collaboratively edit and manage files on remote web servers. 
WebDAV enables functionalities such as file uploads, directory listing, and locking, making it useful for web-based file sharing and remote content management.

# Enumeration

```bash
davtest -url <URL>

cadaver <IP>

# In Metasploit there are some exploits and scanning modules
search webdav
```

## DavTest
**Davtest** try to **upload several files with different extensions** and **check** if the extension is **executed**:
```bash
# Uplaod .txt files and try to move it to other extensions
davtest [-auth user:password] -move -sendbd auto -url http://<IP> 
# Try to upload every extension
davtest [-auth user:password] -sendbd auto -url http://<IP> 
```


# Privilege Escalation
## WMI Service Isolation Local Privilege Escalation Vulnerability - Churrasco
Get the exploit here: [https://github.com/Re4son/Churrasco/](https://github.com/Re4son/Churrasco/)

The Churrasco vulnerability in Windows allows attackers to send malicious SMB requests to a target machine, enabling them to run arbitrary code remotely without needing any authentication.

# References
- [https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/put-method-webdav.html](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/put-method-webdav.html)
- [https://infra.newerasec.com/infrastructure-testing/privilege-esclation/windows/common-exploits](https://infra.newerasec.com/infrastructure-testing/privilege-esclation/windows/common-exploits)
- [https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box-windows/granny/post-exploitation](https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box-windows/granny/post-exploitation)
