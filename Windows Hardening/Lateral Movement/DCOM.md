
# COM

[COM](https://learn.microsoft.com/en-us/windows/win32/com/the-component-object-model?redirectedfrom=MSDN) is, simply put, a method for sharing binary code across different applications and languages.

COM solves all these problems by defining a binary standard, meaning that COM specifies that the binary modules (the DLLs and EXEs) must be compiled to match a specific structure. The standard also specifies exactly how COM objects must be organized in memory. The binaries must also not depend on any feature of any programming language (such as name decoration in C++).

# DCOM

[Specifies the Distributed Component Object Model (DCOM)](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0?redirectedfrom=MSDN) Remote Protocol, which exposes application objects via remote procedure calls ([[135, 593 - RPC]]) and consists of a set of extensions layered on the Microsoft Remote Procedure Call Extensions.

## List DCOM Applications
```powershell
Get-CimInstance Win32_DCOMApplication
```

# Exploitation

A DCOM application **MMC20** allows for snap-in operations. One method withing the application is name `ExecuteShellCommand`. As the name suggests

this allows for command execution over the network.

## Tools
Impacket's `dcomexec.py` script to obtain RCE.

```bash
dcomexec.py <domain>>/<user>:<password>@<ip> <command> -silentcommand -object MMC20
```
*-silentcommand is needed, as otherwise a cmd window will open up on the victim's screen*

# References
- [https://book.hacktricks.wiki/en/windows-hardening/lateral-movement/dcomexec.html](https://book.hacktricks.wiki/en/windows-hardening/lateral-movement/dcomexec.html)
- [https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/](https://enigma0x3.net/2017/01/05/lateral-movement-using-the-mmc20-application-com-object/)
- [https://learn.microsoft.com/en-us/windows/win32/com/the-component-object-model?redirectedfrom=MSDN](https://learn.microsoft.com/en-us/windows/win32/com/the-component-object-model?redirectedfrom=MSDN)
- [https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0?redirectedfrom=MSDN](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-dcom/4a893f3d-bd29-48cd-9f43-d9777a4415b0?redirectedfrom=MSDN)
- [https://help.fortinet.com/fsiem/Public_Resource_Access/7_1_0/rules/PH_RULE_MMC20_Lateral_Movement.htm](https://help.fortinet.com/fsiem/Public_Resource_Access/7_1_0/rules/PH_RULE_MMC20_Lateral_Movement.htm)