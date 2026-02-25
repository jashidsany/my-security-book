# AD ENUMERATION (FROM FOOTHOLD)

### Identify domain

```bash
systeminfo | findstr /B Domain
echo %userdomain%
[System.DirectoryServices.ActiveDirectory.Domain]::GetCurrentDomain()
```

> Confirm you're in a domain

**Why:** Starting point for AD attacks

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

```bash
nltest /dclist:<domain>
nslookup -type=srv _ldap._tcp.dc._msdcs.<domain>
```

> Identify the DC

**Why:** Primary target

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

```bash
net user /domain
Get-ADUser -Filter * | Select SamAccountName
```

> All domain users

**Why:** Build target list

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

```bash
net group /domain
net group 'Domain Admins' /domain
```

> Groups and membership

**Why:** Who is Domain Admin?

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

```bash
net accounts /domain
```

> Lockout threshold, complexity

**Why:** Guides password spray rate

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

```bash
.\SharpHound.exe -c All
or: bloodhound-python -d <domain> -u <user> -p <pass> -ns <DC_IP> -c All
```

> Graph AD relationships

**Why:** Visual attack path finder

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

```bash
Upload .zip to BloodHound GUI
Run: Shortest Path to Domain Admins
Run: Find AS-REP Roastable Users
Run: Find Kerberoastable Users
```

> Query pre-built attack paths

**Why:** Shows exactly how to escalate

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

```bash
Get-ADUser -Filter {ServicePrincipalName -ne '$null'} -Properties ServicePrincipalName
or: impacket-GetUserSPNs <domain>/<user>:<pass> -dc-ip <DC_IP>
```

> Service accounts with SPNs

**Why:** Kerberoast targets

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

```bash
Find-InterestingDomainAcl (PowerView)
or: BloodHound edge analysis
```

> Who has rights over what

**Why:** GenericAll, WriteDACL, etc = takeover

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

```bash
Get-ADComputer -Filter * | Select Name,OperatingSystem
```

> All domain computers

**Why:** Find targets, old OS versions

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

```bash
crackmapexec smb <subnet>/24 --shares -u <user> -p <pass>
```

> Enum all SMB shares

**Why:** Find sensitive files

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
