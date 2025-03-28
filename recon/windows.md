# Tips
## Multiple Users
If there's more than one user that needs to compromised before getting root, enumerate
each user as they might have access to something new that was protected before.

## Temporary Account
If some info suggests that an account is only temporary, then they might have some misconfigurations
or rights that other users don't have.     
Especially if they're created by a user which is in a group that has higher privileges.



# DNS
```bash
# Normal DNS request
dig A @<ip> <domain> 
# Get all available entries
dig any server.local @<DNS_IP>
# Zone transfer without domain
dig axfr @<DNS_IP>
# Zone transfer with domain
dig axfr @<DNS_IP> <DOMAIN>

# Subdomain scan
gobuster dns -d domain.local -t 25 -w <wordlist>

# Normal nmap scan
nmap -sSU -p53 --script dns-nsec-enum --script-args dns-nsec-enum.domains=paypal.com <domain>

# Metasploit
auxiliary/gather/enum_dns
```

## Attacks
### Zone Transfer
DNS servers contain a *Zone* file that replicates the map of the domain.     
Only the server itself should have access to it, but if it's misconfigured anyone can request the file and
get the list of all the sub-domains.

## Ports
```
53  DNS
```


# LDAP
```bash
# Normal enumeration
nmap -sT -Pn -n --open <ip> -p389 --script ldap-rootdse

# Anonymous access
ldapsearch -H ldap://<ip>:<port> -b "dc=domain,dc=local" -x
ldapsearch -H <ip> -x -s base namingcontexts
ldapsearch -H <ip> -x -b "dc=domain,dc=local"
ldapsearch -H <ip> -x -b "dc=domain,dc=local" '(objectClass=person)'
ldapsearch -H <ip> -x -b "dc=domain,dc=local" '(objectClass=user)'
ldapsearch -H <ip> -x -b "dc=domain,dc=local" '(objectClass=group)'

# Connect and enumerate with username and password
ldapsearch -H ldap://<ip> -b "dc=domain,dc=local" -D "cn=username,dc=domain,dc=local" -w <password>   -x
ldapsearch -H <domain.local> -D 'user@domain.local' -w <password> -b "DC=domain,DC=local"

# https://github.com/ropnop/windapsearch
windapsearch.py --dc-ip <ip> -d domain.local -u "" -U
```

## Notes
### Passwords in Result
If an `ldapsearch` query comes back with users, try checking whether they have any password related options set.    

**Example(s)**
```
cascadeLegacyPwd: BASE64 STRING
```

### Info Field
Log of a query might contain some info in the user.     
In many CTF-s they put passwords there.
​
## Ports
```
389     LDAP
636     LDAPS (LDAP over SSL/TLS)
```


# SMB
## Enumeration
```bash
# Get shares
smbmap -H <ip>
smbmap -H <ip> -u <user> -p <password>

# More enumeration
enum4linux -U -o <ip>
enum4linux -a <ip>
nmap --script "safe or smb-enum-*" -p 445 <ip>

# Enumerate users
crackmapexec smb <ip> -u <user> -p <password> --users
crackmapexec smb <ip> -u <user> -p <password> --rid-brute
crackmapexec smb <ip> -u <user> -p <password> --groups
crackmapexec smb <ip> -u <user> -p <password> --local-users

# Null session attack
smbclient -N -L \\\\<ip>
crackmapexec smb <ip> -u ""

# Establishing connection
smbclient //<ip> -U <user>
smbclient.py <domain>/<username>:<password>@<ip>
```

## Ports
```
139     SMB 1.0 session services
138     SMB over UDP
445     SMB main
```

# Kerberos
## GetNPUsers.py
Impacket's `GetNPUsers.py` will attempt to harvest the non-preauth AS_REP responses for a given list of usernames

```bash
GetNPUsers.py domain.local/ -dc-ip <ip> -usersfile <username_file> -format hashcat -outputfile <output>
```

## Kerberoasting
Kerberoasting is a post-exploitation attack technique that attempts to obtain a password hash of an Active Directory account that has a Service Principal Name.
```bash
GetUserSPNs.py -request -dc-ip <ip> domain.local/user -save -outputfile <output_file>
```

### Cracking The Ticket
```bash
hashcat -m 13100 --force <hash_file> <password_file>
```

### Troubleshooting
#### NTLM hash disabled
Use the `-k` option as well as `-dc-host` instead of `-dc-ip`. As the latter will break the authentication and throw an error.

## References
- [https://www.crowdstrike.com/cybersecurity-101/kerberoasting/](https://www.crowdstrike.com/cybersecurity-101/kerberoasting/)

## Ports
```
88      Kerberos key distribution center (KDC)
```


# RPC
## Enumeration
```bash
# USERS
querydispinfo
enumdomusers

## Details
queryuser <rid>
## GGroups
queryusergroups <rid>
## SID
lookupnames <rid>
## Aliases
queryuseraliases builtin <username>


# GROUPS
enumdomgroups
## Details
querygroup <rid>
## Members
querygroupmem <rid>

# SHARES
netshareenumall
## Details
netsharegetinfo <share>
```

## Establishing Connection
```bash
# Null authentication
rpcclient -U '' -N <ip>

# With creds
rpcclient -U <username> <ip>
```

## NIS
```bash
# Install NIS tools
apt-get install nis

# Ping the NIS server to confirm its presence
ypwhich -d <host> <IP>

# Extract user credentials
ypcat –d <host> –h <IP> passwd.byname
```

## Ports
```
135     RPC EPM
445     SMB
593     RPC over HTTPS
```

# Analyse Office Files
Modern `Office` documents are just zip archives with XML files so, just unzip it and look for data within the XML files.

## Unzip
```bash
unzip <file>
```

## oletools
oletools is a package of python tools to analyze Microsoft OLE2 files (also called Structured Storage, Compound File Binary Format or Compound Document File Format), such as Microsoft Office documents or Outlook messages
```bash
# Download tools
sudo pip3 install -U oletools

# Extract macros
olevba -c <file>
```