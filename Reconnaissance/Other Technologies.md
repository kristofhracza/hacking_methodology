# Other Technologies
# SNMP

Simple Network Management Protocol is a protocol used to monitor different devices in the network (like routers, switches, printers, IoTs...).

## Enumeration

```bash
snmpwalk -Os -c public <ip>
snmpcheck <ip>
msf> use auxiliary/scanner/snmp/snmp_enum
```

## Brute-force Community Strings

```bash
hydra -P <wordlist> <host> snmp
onesixtyone -c <wordlist> <ip>
msf> use auxiliary/scanner/snmp/snmp_login
```

## Resources

- [Wordlist for community strings](https://github.com/fuzzdb-project/fuzzdb/blob/master/wordlists-misc/wordlist-common-snmp-community-strings.txt)


# WebDav

## Enumeration

```bash
davtest -url <URL>

# In Metasploit there are some exploits and scanning modules
search webdav
```

## Privilege Escalation
### WMI Service Isolation Local Privilege Escalation Vulnerability - Churrasco
Get the exploit here: [https://github.com/Re4son/Churrasco/](https://github.com/Re4son/Churrasco/)

The Churrasco vulnerability in Windows allows attackers to send malicious SMB requests to a target machine, enabling them to run arbitrary code remotely without needing any authentication.

### References
- [https://infra.newerasec.com/infrastructure-testing/privilege-esclation/windows/common-exploits](https://infra.newerasec.com/infrastructure-testing/privilege-esclation/windows/common-exploits)
- [https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box-windows/granny/post-exploitation](https://manuelvazquez-contact.gitbook.io/oscp-prep/hack-the-box-windows/granny/post-exploitation)



# Apache Subversion (SVN)

Software versioning and revision control system. Read more about it in the [Documentation](https://httpd.apache.org/docs/2.4/)

```sh
# Get files on the server
svn co <url>
```



# QUIC Protocol
*QUIC* is a general-purpose transport layer network protocol

- [Explanation and guide](https://www.debugbear.com/blog/http3-quic-protocol-guide)
- [Wikipedia](https://en.wikipedia.org/wiki/QUIC)

## Access Pages with Curl

```bash
curl --http3 https://site.com
```

## Build Curl From Source

Refer to **[this](https://github.com/curl/curl/blob/master/docs/HTTP3.md#quiche-version)** if your version of `curl` doesn't support QUIC.