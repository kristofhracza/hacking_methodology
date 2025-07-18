# Basic Information
The **File Transfer Protocol (FTP)** is a standard network protocol used to transfer files between a client and a server over a TCP/IP network. Operating primarily over TCP ports 20 and 21, FTP allows users to upload, download, and manage files on remote systems, either anonymously or through authenticated access.

## Connection
### Shell
```bash
ftp <IP>
>anonymous
>anonymous
# List all files (even hidden) (yes, they could be hidden)
>ls -a
# Set transmission to binary instead of ascii
>binary
# Set transmission to ascii instead of binary
>ascii
# Exit
>bye
```

### Browser
Just enter `ftp://anonymous:anonymous@<IP>` to the browser's search bar.

# References
- [https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-ftp/index.html](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-ftp/index.html)