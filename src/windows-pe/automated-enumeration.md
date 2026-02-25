# AUTOMATED ENUMERATION

### Run winPEAS

Most comprehensive auto-enum — Color-coded findings

```bash
.\winPEASx64.exe
or: .\winPEASany.bat
```

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

PowerShell privesc checker — Finds service misconfigs

```bash
Import-Module .\PowerUp.ps1
Invoke-AllChecks
```

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

Detailed system enum — GhostPack tool

```bash
.\Seatbelt.exe -group=all
```

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

C# version of PowerUp — Quick audit

```bash
.\SharpUp.exe audit
```

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

Match patches to exploits — Run systeminfo on target first

```bash
python3 windows-exploit-suggester.py --database <db> --systeminfo <sysinfo.txt>
```

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
