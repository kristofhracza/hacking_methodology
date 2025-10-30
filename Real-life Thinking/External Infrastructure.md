
# Firewall Ruleset Bypass
[https://nmap.org/book/firewall-subversion.html](https://nmap.org/book/firewall-subversion.html)

## Exotic Scan Flags
When a FIN packet is being set, it flies past the rules blocking SYN packets. While a SYN scan only found one open port below 100, the FIN scan finds both of them.
```bash
# FIN flag:  -sF
nmap -sF -T4 <IP>
```


## Source Port Manipulation (UDP - 53)
*Note: Usually comes up with Nessus*

Sometimes, during a normal scan ports can appear to be closed. However, in some cases where the firewall is misconfigured, specifying a source port will reveal the actual state of the port.

This bypass usually only works with port 53 as the source port as the firewall allows traffic through DNS. Though I have seen cases where even a random source port does the trick.
```bash
# Source port flag: -g
nmap -g 53 -sU -p161 <IP>
```
