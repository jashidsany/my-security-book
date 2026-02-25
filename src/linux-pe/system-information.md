# SYSTEM INFORMATION

### OS and kernel version

Check for kernel exploits — Old kernels = easy root

```bash
uname -a
cat /etc/os-release
cat /etc/issue
```

<details>
<summary>Example Output</summary>

```
uname -a
Linux target 4.15.0-20-generic #21-Ubuntu SMP x86_64
(Search: Linux 4.15.0-20 exploit -> DirtyCow, etc)
```
</details>

---

### Who am I / groups

Current user context — docker/lxd/disk groups = root

```bash
id
whoami
groups
```

<details>
<summary>Example Output</summary>

```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
OR: uid=1000(john) gid=1000(john) groups=1000(john),999(docker)
(docker group = instant root!)
```
</details>

---

### All users

Users with shells — Identify targets for lateral movement

```bash
cat /etc/passwd
cat /etc/passwd | grep -v nologin | grep -v false
```

<details>
<summary>Example Output</summary>

```
cat /etc/passwd | grep -v nologin | grep -v false
root:x:0:0:root:/root:/bin/bash
admin:x:1000:1000:Admin:/home/admin:/bin/bash
john:x:1001:1001::/home/john:/bin/bash
(Users with actual shell access)
```
</details>

---

### Readable /etc/shadow

If readable = crack hashes — Should not be world-readable

```bash
cat /etc/shadow 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
cat /etc/shadow
root:$6$rounds=5000$salt$hashhere:19000:0:99999:7:::
admin:$6$salt2$anotherhash:19000:0:99999:7:::
(SHOULD NOT BE READABLE! Crack with hashcat -m 1800)
```
</details>

---

### Network info

Interfaces, routes, connections — Find internal networks, pivot targets

```bash
ip a
ifconfig
route
arp -a
netstat -tulnp
ss -tulnp
```

<details>
<summary>Example Output</summary>

```
ip a
2: eth0: inet 10.10.10.5/24
3: eth1: inet 172.16.1.5/24  <- DUAL HOMED = pivot!

ss -tulnp
127.0.0.1:3306  mysqld  <- internal MySQL
127.0.0.1:8080  python3 <- hidden internal app
0.0.0.0:22      sshd
(Internal services = more attack surface)
```
</details>

---

### Running processes

All running services — Find services running as root

```bash
ps auxww
ps -ef
```

<details>
<summary>Example Output</summary>

```
ps auxww
root  1234 /usr/sbin/apache2 -k start
root  1235 /usr/bin/mysqld --user=root  <- MySQL as root!
root  1236 /opt/vulnerable_app --config /root/app.conf
root  1237 /usr/bin/python3 /opt/cron_script.py
(Custom apps and root processes are targets)
```
</details>

---

### Installed packages

Installed software — Vulnerable versions

```bash
dpkg -l
rpm -qa
```

<details>
<summary>Example Output</summary>

```
dpkg -l | grep -i apache
apache2 2.4.29-1ubuntu4.14
(Old version, check for CVEs)
```
</details>

---

### Environment variables

Credentials in env vars — DB passwords, API keys

```bash
env
cat /proc/*/environ 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
env
DB_PASSWORD=MySQLr00tP@ss
API_KEY=sk-abc123456
SECRET_KEY=SuperSecretKeyHere
(Credentials exposed in environment)
```
</details>

---
