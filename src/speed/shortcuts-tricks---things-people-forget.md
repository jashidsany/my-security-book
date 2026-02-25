# SHORTCUTS & TRICKS - Things people forget

### Check all HTTP ports `Prevents missed foothold`

**Trick** — Many people only check 80/443 and miss the actual attack surface

```bash
Always browse EVERY open port in a browser. Even weird ports like 5000, 8080, 8443, 9090 often host web apps
```

<details>
<summary>Example Output</summary>

```
nmap shows: 80, 5000, 8443
Port 80: default Apache page
Port 5000: Flask app with login
Port 8443: Jenkins (default creds!)
```
</details>

---

### Screenshot tool for many ports `~5 min for 10+ ports`

**Trick** — Auto-screenshot every HTTP service. Visual triage is 10x faster than curl'ing each one

```bash
gowitness nmap -f loot/tcp.txt --open
OR: eyewitness --no-prompt -f urls.txt
```

<details>
<summary>Example Output</summary>

```
Screenshots saved to report/
Quick scroll reveals:
- Default pages (skip)
- Login pages (try defaults)
- Custom apps (enumerate)
```
</details>

---

### Source code ALWAYS `~30 sec per page`

**Trick** — Comments with creds are more common than you think. Check source before running any tools

```bash
On EVERY web page: Ctrl+U -> search for:
<!-- (comments)
password
admin
token
secret
api
hidden
TODO
BUG
```

<details>
<summary>Example Output</summary>

```
<!-- Debug: admin/admin123 -->
<!-- TODO: remove test account -->
<input type='hidden' name='role' value='user'>
```
</details>

---

### Check for virtual hosting first `Prevents missed content`

**Trick** — If the box has a hostname, ALWAYS add it to /etc/hosts and browse again. Different site = different attack surface

```bash
curl -s http://$IP vs curl -s -H 'Host: hostname.htb' http://$IP
If different content: virtual hosting
```

<details>
<summary>Example Output</summary>

```
http://10.10.10.5 -> Apache default
http://target.htb -> Custom web app
(Entire app was hidden behind vhost)
```
</details>

---

### Nmap script scan targeted `~2 min`

**Trick** — Targeted vuln scripts are faster than -sC and find things like EternalBlue, Shellshock automatically

```bash
nmap --script 'vuln and safe' -p $PORTS $IP
OR per-service:
nmap --script smb-vuln* -p 445 $IP
nmap --script http-vuln* -p 80 $IP
```

<details>
<summary>Example Output</summary>

```
| smb-vuln-ms17-010: VULNERABLE
(Instant win - no further enum needed)
```
</details>

---

### Check searchsploit EXACT version `~30 sec`

**Trick** — Use exact version string. If no results, broaden one number at a time

```bash
searchsploit apache 2.4.49
NOT: searchsploit apache (too broad)
NOT: searchsploit apache 2 (too many results)
```

<details>
<summary>Example Output</summary>

```
Apache 2.4.49 - Path Traversal (CVE-2021-41773)
Exactly 1 result = likely the intended path
```
</details>

---

### Test credentials immediately `Potentially hours`

**Trick** — Password reuse is the #1 easy win. Every minute spent enumerating after finding creds could be wasted

```bash
Found creds on ANY service? Stop everything.
Test on: SSH, WinRM, SMB, RDP, web login, FTP, MSSQL, MySQL
Do this BEFORE continuing enum
```

<details>
<summary>Example Output</summary>

```
Found db_user:DBp@ss in config.php
SSH: FAILED
SMB: WORKS! -> shell as db_user
(Skipped entire web exploitation path)
```
</details>

---

### Quick LFI/SQLi test on every param `~30 sec per param`

**Trick** — Takes 30 seconds per parameter. Test ALL params including POST data, cookies, headers

```bash
For EVERY parameter you see in URLs:
?id=1' (SQLi test)
?page=../../../../etc/passwd (LFI test)
?search=<script>alert(1)</script> (XSS test)
?cmd=;id (command injection test)
```

<details>
<summary>Example Output</summary>

```
http://target/page.php?lang=en
Try: ?lang=../../../../etc/passwd
Result: root:x:0:0:root...
(LFI in a param you'd never fuzz)
```
</details>

---
