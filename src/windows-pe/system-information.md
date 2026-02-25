# SYSTEM INFORMATION

### System info

OS version, patches, architecture — Check for missing patches

```bash
systeminfo
hostname
whoami /all
```

<details>
<summary>Example Output</summary>

```
systeminfo
OS Name: Microsoft Windows Server 2019
OS Version: 10.0.17763 N/A Build 17763
System Type: x64-based PC
Hotfix(s): 3 Hotfix(s) Installed
  KB4534273, KB4516115, KB4523204
```
</details>

---

### Current user privileges

Token privileges — SeImpersonate = potato attack

```bash
whoami /priv
```

<details>
<summary>Example Output</summary>

```
whoami /priv

SECURITY INFORMATION
--------------------
SeImpersonatePrivilege  Enabled   <- POTATO ATTACK!
SeAssignPrimaryToken    Enabled
SeBackupPrivilege       Enabled   <- READ ANY FILE!
```
</details>

---

### Users and groups

Local accounts — Who is admin?

```bash
net user
net localgroup
net localgroup administrators
```

<details>
<summary>Example Output</summary>

```
net user
Administrator  Guest  john  svc_backup

net localgroup administrators
Administrator
john
(john is local admin!)
```
</details>

---

### Network info

Interfaces, routes, connections — Internal networks, pivot targets

```bash
ipconfig /all
route print
arp -a
netstat -ano
```

<details>
<summary>Example Output</summary>

```
ipconfig /all
Ethernet0: 10.10.10.5/24  Gateway: 10.10.10.1
Ethernet1: 172.16.1.5/24  <- DUAL HOMED!

netstat -ano | findstr LISTEN
TCP  0.0.0.0:80      LISTENING  1234
TCP  127.0.0.1:8080  LISTENING  5678  <- internal only!
TCP  0.0.0.0:3389    LISTENING  904
```
</details>

---

### Running processes

Services and processes — Find interesting services

```bash
tasklist /svc
Get-Process
```

<details>
<summary>Example Output</summary>

```
tasklist /svc
Image Name         PID  Services
httpd.exe          1234 Apache2.4
mysqld.exe         1235 MySQL
FileZilla Serv     1236 FileZilla  <- check version!
custom_svc.exe     1237 CustomSvc  <- non-default!

wmic process get name,executablepath
(Find custom/vulnerable services)
```
</details>

---

### Installed software

Installed applications — Outdated software with CVEs

```bash
wmic product get name,version
Get-ItemProperty 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*' | Select DisplayName,DisplayVersion
```

<details>
<summary>Example Output</summary>

```
wmic product get name,version
Apache HTTP Server  2.4.29
MySQL Server        5.7.29
FileZilla Server    0.9.41  <- OLD VERSION!
(Search each for known CVEs)
```
</details>

---

### Check architecture

32-bit or 64-bit — Match exploits to arch

```bash
wmic os get osarchitecture
```

<details>
<summary>Example Output</summary>

```
wmic os get osarchitecture
64-bit
(Use x64 exploits and payloads)
```
</details>

---

### Hotfixes installed

Installed patches — Missing KB = potential exploit

```bash
wmic qfe list
Get-HotFix
```

<details>
<summary>Example Output</summary>

```
wmic qfe list
HotFixID   InstalledOn
KB4534273  1/15/2020
KB4516115  9/10/2019
(Last patch 2020 = many missing updates)
```
</details>

---
