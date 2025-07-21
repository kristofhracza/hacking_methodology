# BloodHound
BloodHound is used to visualise AD environments and discover attack paths.
[Documentation](https://bloodhound.readthedocs.io/en/latest/index.html)

## Ingestors
### SharpHound
Local data collector for BloodHound
- [Documentation](https://bloodhound.readthedocs.io/en/latest/data-collection/sharphound.html)
- [Releases](https://github.com/BloodHoundAD/BloodHound/tree/master/Collectors)

```powershell
./SharpHound.exe --CollectionMethods All
Invoke-BloodHound -CollectionMethod All
```

### bloodhound.py

Python based data collection tool for BloodHound
This will run against the domain, so can one run it from a remote machine.

```bash
bloodhound-python -u <username> -p <password> -d <domain> -c All -ns <nameserver_ip>
```
 [Documentation and Release](https://github.com/fox-it/BloodHound.py)

# Powerview
Powershell based reconnaissance tool.
## Source
- [https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1)
## Commands
### Domain Information
```powershell
# Basic domain info
Get-NetDomain

# Computers
Get-NetComputer | select samaccountname, operatingsystem
## Find computers with Constrained Delegation
Get-NetComputer -TrustedToAuth | select samaccountname # Find computers with Constrained Delegation
## Find any machine accounts in privileged groups
Get-DomainGroup -AdminCount | Get-DomainGroupMember -Recurse | ?{$_.MemberName -like '*$'}
```

### Users
```powershell
# Basic user enabled infos
Get-NetUser -UACFilter NOT_ACCOUNTDISABLE | select samaccountname, description, pwdlastset, logoncount, badpwdcount

# Groups
Get-NetGroup | select samaccountname, admincount, description

# Check if any user passwords are set
$FormatEnumerationLimit=-1;Get-DomainUser -LDAPFilter '(userPassword=*)' -Properties samaccountname,memberof,userPassword | % {Add-Member -InputObject $_ NoteProperty 'Password' "$([System.Text.Encoding]::ASCII.GetString($_.userPassword))" -PassThru} | fl
```

### ACL
```powershell
Invoke-ACLScanner -ResolveGUIDs | select IdentityReferenceName, ObjectDN, ActiveDirectoryRights | fl
Find-InterestingDomainAcl
Find-InterestingDomainAcl -Domain your.domain -ResolveGUIDs
## For a specific user
Find-InterestingDomainAcl -ResolveGUIDs | ?{$_.IdentityReferenceName -match "USERNAME"}
```

# References
- [https://powersploit.readthedocs.io/en/latest/Recon/](https://powersploit.readthedocs.io/en/latest/Recon/)