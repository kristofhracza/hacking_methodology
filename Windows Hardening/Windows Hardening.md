# System Information
- [ ] Check basic [[Windows Hardening/Commands and DIRs#Directories|directories]]
- [ ] Environment variables
- [ ] Read [[Windows Hardening/Commands and DIRs#PowerShell History| PowerShell history]]
- [ ] Look for [[Windows Hardening/Commands and DIRs#Drives|Drives]]
- [ ] Run: 
	- [ ] [winPeas]([peass-ng/PEASS-ng: PEASS - Privilege Escalation Awesome Scripts SUITE (with colors)](https://github.com/peass-ng/PEASS-ng)) -- If AV catches it, try running it from memory.
	- [ ] [HardeningKitty](https://github.com/scipag/HardeningKitty)
	- [ ] `sysinfo` on the host and take the output and feed it to [Windows-Exploit-Suggester](https://github.com/AonCyberLabs/Windows-Exploit-Suggester)
		-  *It's aslo a Metasploit module*

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
- [ ] Check [[Windows Hardening/Path Hijacking|Path Hijacking]]

# Applications
- [ ] Vulnerable [[Windows Hardening/Commands and DIRs#Drivers|Drivers]]


# Everything Needed For The Checklist and Beyond
- [[Windows Hardening/Commands and DIRs|Commands and DIRs]]
- Active Directory
	- [[ASREPRoast]]
	- [[DCSync]]
	- [[Enumeration Tools]]
	- [[Golden Ticket]]
	- [[LAPS]]
	- [[Silver Ticket]]
- Lateral Movement
	- [[DCOM]]
- [[AV Evasion]]
- [[Windows Hardening/Path Hijacking|Path Hijacking]]