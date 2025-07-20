# System Information
- [ ] Check basic [[Windows Hardening/Commands and DIRs#Directories|directories]]
- [ ] Environment variables
- [ ] Read [[Windows Hardening/Commands and DIRs#PowerShell History| PowerShell history]]
- [ ] Look for [[Windows Hardening/Commands and DIRs#Drives|Drives]]
- [ ] Run WinPeas

# User Information
- [ ] Gather [[Windows Hardening/Commands and DIRs#User Information|user information]]
	- [ ] ID
	- [ ] Groups
		- [ ] Is the group unusual?
	- [ ] Files
- [ ] Clipboards
- [ ] Password Policy
- [ ] Used [[Windows Hardening/Commands and DIRs#accesschk - Enumeration|accesschk]]

# Windows Credentials
- [ ] [[Windows Hardening/Commands and DIRs#Winlogon Credentials|Winlogon]]
- [ ]  [[Windows Hardening/Commands and DIRs#Credentials manager / Windows vault|Windows Vault]]
- [ ] [[Windows Hardening/Commands and DIRs#PowerShell Credentials|Powershell Credentials]]
- [ ] [[Windows Hardening/Commands and DIRs#Remote Desktop Credentials Manager|Remote Desktop Credentials Manager]]
- [ ] [[Windows Hardening/Commands and DIRs#Sticky Notes|Sticky Notes]]

# Network
- [ ] Enumerate [[Windows Hardening/Commands and DIRs#Network|network information]]
- [ ] Look at services running on `127.0.0.1`

# DLL Hijacking
- [ ]  Can you **write in any folder inside PATH**?
- [ ]  Is there any known service binary that **tries to load any non-existent DLL**?
- [ ]  Can you **write** in any **binaries folder**?

# Applications
- [ ] Vulnerable [[Windows Hardening/Commands and DIRs#Drivers|Drivers]]


# Everything Needed For The Checklist
- [[Windows Hardening/Commands and DIRs|Commands and DIRs]]
