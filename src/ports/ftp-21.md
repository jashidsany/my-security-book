# FTP (21)

### Anonymous login

```bash
ftp $IP
Username: anonymous
Password: (blank)
```

> Try blank password and email@email.com

**Why:** Misconfigured FTP servers

<details>
<summary>Example Output</summary>

```
230 Login successful.
ftp> ls
-rw-r--r--  1 0  0   1024 Jan 01 config.txt
-rw-r--r--  1 0  0    512 Jan 01 backup.zip
```
</details>

---

### List all files including hidden

```bash
ls -la
cd ..
ls -la
```

> Check every directory

**Why:** Hidden files with creds

<details>
<summary>Example Output</summary>

```
drwxr-xr-x  2 0 0  4096 Jan 01 .
drwxr-xr-x  3 0 0  4096 Jan 01 ..
-rw-r--r--  1 0 0   220 Jan 01 .bash_history
-rw-r--r--  1 0 0  1679 Jan 01 .id_rsa
```
</details>

---

### Download everything

```bash
wget -r ftp://anonymous@$IP/
```

> Recursive download

**Why:** Grab all files for offline review

<details>
<summary>Example Output</summary>

```
wget -r ftp://anonymous@10.10.10.5/
Downloaded: 12 files, 45K
./10.10.10.5/backup.zip
./10.10.10.5/.hidden/credentials.txt
(Check every file offline)
```
</details>

---

### Check for write access

```bash
put test.txt
```

> Can you upload? webshell potential

**Why:** Upload to web root = RCE

<details>
<summary>Example Output</summary>

```
ftp> put test.txt
226 Transfer complete.
(SUCCESS = you can upload webshells)
```
</details>

---

### Brute force creds

```bash
hydra -L users.txt -P passwords.txt ftp://$IP
```

> If anonymous fails

**Why:** Default/weak creds

<details>
<summary>Example Output</summary>

```
hydra -L users.txt -P passwords.txt ftp://10.10.10.5
[21][ftp] host: 10.10.10.5 login: admin password: admin123
(FOUND VALID CREDENTIALS)
```
</details>

---

### Check FTP version for exploits

```bash
searchsploit vsftpd
searchsploit proftpd
```

> Version from nmap output

**Why:** vsftpd 2.3.4 backdoor, ProFTPD exploits

<details>
<summary>Example Output</summary>

```
searchsploit vsftpd
vsftpd 2.3.4 - Backdoor Command Execution
(If version matches = instant shell)
```
</details>

---
