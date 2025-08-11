# Overview
It is a client/server system that allows users to access files across a network and treat them as if they resided in a local file directory.

# Enumeration

## nmap scripts
```bash
# List NFS exports and check permissions
nfs-ls
# Like showmount -e
nfs-showmount
# Disk statistics and info from NFS share
nfs-statfs
```

# Mounting
```bash
# Look at available folders
showmount -e <IP>

# Mount folder
mount -t nfs [-o vers=2] <ip>:<remote_folder> <local_folder> -o nolock
```
You should specify to **use version 2** because it doesn't have **any** **authentication** or **authorization**.


# References
- [https://hacktricks.boitatech.com.br/pentesting/nfs-service-pentesting](https://hacktricks.boitatech.com.br/pentesting/nfs-service-pentesting)
- [https://book.hacktricks.wiki/en/network-services-pentesting/nfs-service-pentesting.html](https://book.hacktricks.wiki/en/network-services-pentesting/nfs-service-pentesting.html)