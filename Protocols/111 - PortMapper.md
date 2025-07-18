# Basic Information
The **PortMapper** service, also known as *rpcbind*, is an essential component of the [[135, 593 - RPC]] protocol suite that listens on TCP and UDP port 111. It maps RPC program numbers to their corresponding dynamic port numbers, allowing clients to discover which port a specific RPC service is using on a server.

# Enumeration
```bash
rpcinfo <DOMAIN> 
nmap -sSUC -p111 <IP>
```

# NIS
Exploring NIS vulnerabilities typically involves a two-step process, beginning with the identification of the `ypbind` service. A critical aspect of this process is discovering the NIS domain name.
The assessment starts by installing the required tools *(`apt-get install nis`)*. 
The next step involves using `ypwhich` to verify the presence of an NIS server by querying it with the domain name and server IP.
The final stage employs the `ypcat` command to enumerate sensitive information, most notably the password map containing encrypted user credentials. 
```bash
# Install NIS tools
apt-get install nis

# Ping the NIS server to confirm its presence
ypwhich -d <DOMAIN> <IP>

# Extract user credentials
ypcat –d
```

## NIF Files
| Master file         | Map(s)                         | Notes                          |
|---------------------|--------------------------------|--------------------------------|
| /etc/hosts          | hosts.byname, hosts.byaddr     | Contains hostnames and IP details |
| /etc/passwd         | passwd.byname, passwd.byuid    | NIS user password file         |
| /etc/group          | group.byname, group.bygid      | NIS group file                 |
| /usr/lib/aliases    | mail.aliases                   | Details mail aliases           |



# References
- [https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-rpcbind.html](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-rpcbind.html)