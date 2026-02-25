# AUTOMATED ENUMERATION

### Run winPEAS

```bash
.\winPEASx64.exe
or: .\winPEASany.bat
```

> Most comprehensive auto-enum

**Why:** Color-coded findings

<details>
<summary>Example Output</summary>

```
winPEAS output:
[!] Possible privesc:
  SeImpersonatePrivilege: ENABLED
[!] Unquoted service path:
  C:\Program Files\My App\service.exe
[!] Writable service:
  CustomSvc -> C:\custom\svc.exe
```
</details>

---

### PowerUp.ps1

```bash
Import-Module .\PowerUp.ps1
Invoke-AllChecks
```

> PowerShell privesc checker

**Why:** Finds service misconfigs

<details>
<summary>Example Output</summary>

```
Import-Module .\PowerUp.ps1
Invoke-AllChecks

[*] Checking service permissions...
[!] CustomSvc - Users have AllAccess
[*] Checking unquoted service paths...
[!] VulnService - C:\Program Files\My App\svc.exe
```
</details>

---

### Seatbelt

```bash
.\Seatbelt.exe -group=all
```

> Detailed system enum

**Why:** GhostPack tool

<details>
<summary>Example Output</summary>

```
.\Seatbelt.exe -group=all
====== InterestingFiles ======
  C:\Users\admin\Desktop\passwords.txt
====== TokenPrivileges ======
  SeImpersonatePrivilege: Enabled
====== SavedRDPConnections ======
  DC01.corp.local - admin
```
</details>

---

### SharpUp

```bash
.\SharpUp.exe audit
```

> C# version of PowerUp

**Why:** Quick audit

<details>
<summary>Example Output</summary>

```
.\SharpUp.exe audit
=== Modifiable Services ===
Name: CustomSvc
Path: C:\custom\svc.exe
Permissions: Everyone [AllAccess]
(Can change service binary path)
```
</details>

---

### windows-exploit-suggester

```bash
python3 windows-exploit-suggester.py --database <db> --systeminfo <sysinfo.txt>
```

> Match patches to exploits

**Why:** Run systeminfo on target first

<details>
<summary>Example Output</summary>

```
python3 wes.py sysinfo.txt
[E] MS17-010: EternalBlue (KB4013389)
[E] CVE-2019-1458: WizardOpium (KB4530684)
[E] CVE-2021-36934: HiveNightmare
[M] MS16-032: Secondary Logon (KB3143141)
```
</details>

---
