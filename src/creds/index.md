# 🔑 Credential Tracking

Where to find credentials and how to track them across an engagement.

---

## General

### admin123

FTP anonymous share /config.txt — SSH, SMB, WinRM, Web login, RDP, MySQL

```bash
Plaintext
```

<details>
<summary>Example Output</summary>

```
SSH: YES, SMB: YES, WinRM: NO, Web: YES, RDP: NO, MySQL: NO
```
</details>

---

### $krb5tgs$23$*john*...

Kerberoasting via GetUserSPNs — Cracking: hashcat -m 13100

```bash
Kerberoast TGS
```

<details>
<summary>Example Output</summary>

```
CRACKED: Summer2024!
```
</details>

---

### 31d6cfe0d16ae931b...

SAM dump from SeBackupPrivilege — PTH: crackmapexec entire /24 subnet

```bash
NTLM hash
```

<details>
<summary>Example Output</summary>

```
Admin on DC01, WEB01, DB01
```
</details>

---

### sa

MSSQL default credentials — xp_cmdshell for RCE

```bash
Default cred
```

<details>
<summary>Example Output</summary>

```
SYSTEM shell via MSSQL
```
</details>

---

### SSH

WinRM — RDP

```bash
SMB
```

<details>
<summary>Example Output</summary>

```
FTP
```
</details>

---

### web.config, wp-config.php, .env, database.yml, settings.py, config.php

Always check /var/www, /opt, /srv, /etc

```bash
DB creds, API keys, secret keys
```

---

### HTML comments, JavaScript files, hidden form fields

View source on EVERY page

```bash
Hardcoded passwords, API tokens, debug creds
```

---

### .bash_history, .mysql_history, ConsoleHost_history.txt

Check for ALL users, not just current

```bash
Commands with passwords typed inline
```

---

### SMB shares, FTP files, NFS exports

Download and search EVERYTHING

```bash
Config backups, credential files, SSH keys
```

---

### Process command lines via snmpwalk

Full walk with: 1.3.6.1.2.1.25.4.2.1.5

```bash
Passwords passed as command arguments
```

---

### User description fields

Search: (description=*pass*)

```bash
Admins put temp passwords in descriptions
```

---

### AutoLogon, VNC, PuTTY, WiFi passwords

reg query HKLM /f password /t REG_SZ /s

```bash
Saved plaintext credentials
```

---

### POP3/IMAP mailboxes

Login with found creds, read ALL emails

```bash
Password reset emails, shared credentials
```

---

### User tables, config tables

SELECT * FROM users always

```bash
Hashed or plaintext passwords
```

---

### AS-REP roasting, Kerberoasting

Always try both with every domain cred found

```bash
Crackable hashes without needing creds (AS-REP) or with any domain cred (Kerberoast)
```

---
