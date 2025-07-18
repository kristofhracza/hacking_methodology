
# Basic Information
The **Domain Name System (DNS)** is a hierarchical and decentralised naming system that translates human-readable domain names (e.g., google.com) into machine-readable IP addresses, enabling devices to locate and communicate with each other over the internet.

# Enumeration

## Banner Grabbing

```bash
dig version.bind CHAOS TXT@DNS
```


## Any Records
```bash
# Get all available entries
dig any server.local @<DNS_IP>
```


## Zone Transfer
DNS servers contain a *Zone* file that replicates the map of the domain.
Only the server itself should have access to it, but if it's misconfigured anyone can request the file and get the list of all the sub-domains.
```bash
# Zone transfer without domain
dig axfr @<IP>

# Zone transfer with domain
dig axfr @<IP> <DOMAIN>
```

## More Information
```bash
# Normal request
dig A @<IP> <DOMAIN>

# Info
dig TXT @<IP> <DOMAIN>
```

## Other Scans
```bash
# Subdomain scan
gobuster dns -d domain.local -t 25 -w <wordlist>

# Metasploit
auxiliary/gather/enum_dns
```

# References
- [https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-dns.html](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-dns.html)
- [https://learn.microsoft.com/en-us/windows-server/networking/dns/zone-types](https://learn.microsoft.com/en-us/windows-server/networking/dns/zone-types)
- [https://www.acunetix.com/blog/articles/dns-zone-transfers-axfr/](https://www.acunetix.com/blog/articles/dns-zone-transfers-axfr/)