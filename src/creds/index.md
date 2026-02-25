# 🔑 Credential Tracking

Where to find credentials and how to track them across an engagement.

---

## General

### admin123

```bash
Plaintext
```

> FTP anonymous share /config.txt

**Why:** SSH, SMB, WinRM, Web login, RDP, MySQL

<details>
<summary>Example Output</summary>

```
SSH: YES, SMB: YES, WinRM: NO, Web: YES, RDP: NO, MySQL: NO
```
</details>

---

### $krb5tgs$23$*john*...

```bash
Kerberoast TGS
```

> Kerberoasting via GetUserSPNs

**Why:** Cracking: hashcat -m 13100

<details>
<summary>Example Output</summary>

```
CRACKED: Summer2024!
```
</details>

---

### 31d6cfe0d16ae931b...

```bash
NTLM hash
```

> SAM dump from SeBackupPrivilege

**Why:** PTH: crackmapexec entire /24 subnet

<details>
<summary>Example Output</summary>

```
Admin on DC01, WEB01, DB01
```
</details>

---

### sa

```bash
Default cred
```

> MSSQL default credentials

**Why:** xp_cmdshell for RCE

<details>
<summary>Example Output</summary>

```
SYSTEM shell via MSSQL
```
</details>

---

### SSH

```bash
SMB
```

> WinRM

**Why:** RDP

<details>
<summary>Example Output</summary>

```
FTP
```
</details>

---

### web.config, wp-config.php, .env, database.yml, settings.py, config.php

```bash
DB creds, API keys, secret keys
```

> Always check /var/www, /opt, /srv, /etc

---

### HTML comments, JavaScript files, hidden form fields

```bash
Hardcoded passwords, API tokens, debug creds
```

> View source on EVERY page

---

### .bash_history, .mysql_history, ConsoleHost_history.txt

```bash
Commands with passwords typed inline
```

> Check for ALL users, not just current

---

### SMB shares, FTP files, NFS exports

```bash
Config backups, credential files, SSH keys
```

> Download and search EVERYTHING

---

### Process command lines via snmpwalk

```bash
Passwords passed as command arguments
```

> Full walk with: 1.3.6.1.2.1.25.4.2.1.5

---

### User description fields

```bash
Admins put temp passwords in descriptions
```

> Search: (description=*pass*)

---

### AutoLogon, VNC, PuTTY, WiFi passwords

```bash
Saved plaintext credentials
```

> reg query HKLM /f password /t REG_SZ /s

---

### POP3/IMAP mailboxes

```bash
Password reset emails, shared credentials
```

> Login with found creds, read ALL emails

---

### User tables, config tables

```bash
Hashed or plaintext passwords
```

> SELECT * FROM users always

---

### AS-REP roasting, Kerberoasting

```bash
Crackable hashes without needing creds (AS-REP) or with any domain cred (Kerberoast)
```

> Always try both with every domain cred found

---
