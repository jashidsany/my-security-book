# 🕳️ Rabbit Hole Warnings

Common traps and false positives that waste hours on OSCP.

---

## COMMON RABBIT HOLES - Patterns that waste time on OSCP

### Brute forcing SSH without valid usernames 🕐 `1-3 hours`

**Service:** SSH

**Why it's a trap:** SSH brute force is extremely slow and almost never the intended OSCP path. SSH is 99% of the time a 'use found creds here' entry point

**Do this instead:** Find usernames from other services first (SMTP enum, web app, SMB, SNMP). Only brute force SSH with a confirmed valid username

> 🚩 **Red flag:** You're running hydra on SSH with 'admin' or a huge username list as your first move

---

### Trying SSH exploits on modern versions 🕐 `30-60 min`

**Service:** SSH

**Why it's a trap:** OpenSSH 7.6+ is almost never exploitable. User enumeration (CVE-2018-15473) only works on 7.2-7.7

**Do this instead:** Note the version, move on. SSH is for credential reuse, not exploitation on OSCP

> 🚩 **Red flag:** Spending 30+ min searching for SSH exploits on OpenSSH 8.x

---

### Running huge wordlists on gobuster 🕐 `15-30 min`

**Service:** Web

**Why it's a trap:** directory-list-2.3-medium has 220k entries. Running it with 5 extensions = 1.1M requests. Takes 20+ min and you're just waiting

**Do this instead:** Start with common.txt (4600 entries). Only escalate to medium if common finds nothing. Use raft-medium-directories not dirbuster

> 🚩 **Red flag:** You've been watching gobuster scroll for 15 min and found nothing beyond what common.txt would find

---

### Deep fuzzing every parameter 🕐 `30-60 min`

**Service:** Web

**Why it's a trap:** ffuf with huge wordlists on every parameter hoping to find hidden values. Most parameters just take their expected input

**Do this instead:** Quick manual test (5 values) on each param. Focus on params that affect page behavior (id=, page=, file=, lang=, user=)

> 🚩 **Red flag:** You're fuzzing a 'sort=asc/desc' parameter with 10k values

---

### Exploiting XSS on OSCP 🕐 `30-60 min`

**Service:** Web

**Why it's a trap:** XSS very rarely leads to a foothold on OSCP. There's no 'victim browser' to receive your XSS payload in most cases

**Do this instead:** Focus on SQLi, LFI, file upload, command injection, SSTI. These give direct code execution

> 🚩 **Red flag:** You found reflected XSS and are trying to escalate it to RCE

---

### WordPress plugin rabbit holes 🕐 `30-60 min`

**Service:** Web

**Why it's a trap:** wpscan finds 50 'vulnerabilities' but most require auth or are informational/low severity

**Do this instead:** Focus on: unauthenticated RCE, file upload, SQLi. Ignore XSS, CSRF, and 'requires admin' vulns. Check wp-admin with found creds first

> 🚩 **Red flag:** Reading through 50 wpscan findings one by one

---

### Trying SQLMap when manual fails 🕐 `20-40 min`

**Service:** Web

**Why it's a trap:** If manual SQLi testing shows no injection point, SQLMap won't find one either. Also SQLMap is banned on OSCP exam

**Do this instead:** If manual testing (adding ' and checking for errors/behavior changes) shows nothing, the param probably isn't injectable. Move on

> 🚩 **Red flag:** You've tried SQLMap with --level 5 --risk 3 on every parameter

---

### Trying to crack NTLMv2 hashes from Responder 🕐 `30-120 min`

**Service:** SMB

**Why it's a trap:** NTLMv2 hashes only crack if the password is in your wordlist. Complex passwords can take hours/days

**Do this instead:** Try rockyou.txt for 5 min max. If it doesn't crack, try relay attacks instead (ntlmrelayx) or move to other vectors

> 🚩 **Red flag:** hashcat has been running for 30+ min on an NTLMv2 hash

---

### Exhaustive share enumeration with no creds 🕐 `20-30 min`

**Service:** SMB

**Why it's a trap:** If null session fails and you have no creds, you've learned everything SMB will tell you for now

**Do this instead:** Note SMB exists, note if signing is disabled (for relay), move on. Come back with creds

> 🚩 **Red flag:** Running multiple SMB tools hoping one of them gets access when null session already failed

---

### Trying every kernel exploit 🕐 `30-60 min`

**Service:** PrivEsc

**Why it's a trap:** Kernel exploits are unreliable. They can crash the box, and compiling/transferring takes time. Most OSCP boxes have an intended non-kernel privesc

**Do this instead:** Check sudo, SUID, cron, capabilities, config files FIRST. Kernel exploits are last resort

> 🚩 **Red flag:** You're on your 3rd kernel exploit compilation and none have worked

---

### Chasing processes that need interaction 🕐 `30-60 min`

**Service:** PrivEsc

**Why it's a trap:** Seeing a process run by root doesn't always mean you can exploit it. If it requires specific input or conditions, it might not be the path

**Do this instead:** Focus on what YOU can control: writable files, writable cron scripts, misconfigurations. Check pspy for scheduled tasks you can influence

> 🚩 **Red flag:** You're trying to reverse engineer a custom binary instead of checking file permissions

---

### Deep binary reverse engineering 🕐 `30-60 min`

**Service:** PrivEsc

**Why it's a trap:** OSCP rarely requires actual reverse engineering. If a custom SUID binary exists, the exploit is usually simple (PATH abuse, library hijack, buffer overflow with clear pattern)

**Do this instead:** Run strings on the binary. Check ltrace/strace. Look for relative paths, missing libraries, or obvious input handling

> 🚩 **Red flag:** You've loaded the binary in Ghidra and are reading assembly for 30+ min

---

### Trying to crack Kerberoast hashes forever 🕐 `30-60 min`

**Service:** AD

**Why it's a trap:** Some service accounts have strong passwords. If rockyou.txt doesn't crack it in 5 min, try a bigger list for 10 more min, then move on

**Do this instead:** Check for AS-REP roastable accounts (easier to crack). Try password spraying. Look for other paths in BloodHound

> 🚩 **Red flag:** hashcat running 30+ min on a TGS hash

---

### Enumerating AD without BloodHound 🕐 `20-60 min`

**Service:** AD

**Why it's a trap:** Manual AD enumeration misses ACL-based attack paths that BloodHound visualizes in seconds. You'll waste hours finding what BloodHound shows instantly

**Do this instead:** Always run SharpHound and load into BloodHound first. Use the built-in queries: 'Shortest Path to DA', 'Kerberoastable Users'

> 🚩 **Red flag:** You're running net user /domain and net group /domain manually for 20 min

---

### Overthinking the box 🕐 `30-120 min`

**Service:** General

**Why it's a trap:** OSCP boxes have intended paths. If you're constructing a 5-step exploit chain, you're probably overcomplicating it

**Do this instead:** The answer is usually simpler than you think. Re-enumerate. Check what you missed. The foothold is often in plain sight

> 🚩 **Red flag:** You're chaining 3 vulnerabilities together and it's still not working

---

### Not checking UDP 🕐 `0 min (but costs you the box)`

**Service:** General

**Why it's a trap:** SNMP (161) and TFTP (69) are commonly on UDP and provide easy wins. Many people skip UDP entirely

**Do this instead:** Always run: nmap -sU --top-ports 20 $IP. Takes 2 minutes. SNMP can reveal usernames, passwords in process command lines

> 🚩 **Red flag:** You're stuck and haven't checked UDP yet

---

### Forgetting /etc/hosts 🕐 `30-120 min`

**Service:** General

**Why it's a trap:** Browsing by IP shows Apache default page. Browsing by hostname shows the actual web app. Different vhosts = completely different attack surface

**Do this instead:** ALWAYS add hostnames to /etc/hosts: IP, nmap hostname, SSL cert names, any domain found in enumeration

> 🚩 **Red flag:** You see 'Apache2 Ubuntu Default Page' and think there's no web app

---

## FALSE POSITIVE PATTERNS - Things that LOOK promising but aren't

### Filtered ports showing in scan 🕐 `10-20 min`

**Service:** Nmap

**Why it's a trap:** Filtered usually means firewall is dropping packets. The service may not actually be running or accessible

**Do this instead:** Focus on OPEN ports. Come back to filtered only if you've exhausted all open ports and found a firewall bypass

> 🚩 **Red flag:** 'Filtered' in nmap makes you think there's a hidden service to find

---

### Tons of open ports (1000+) 🕐 `20-30 min`

**Service:** Nmap

**Why it's a trap:** Could be a firewall returning SYN/ACK for everything (false positives). Or it's a real box with many services

**Do this instead:** Verify with: nmap -sV on suspicious ports. If no version detected = probably fake. Focus on ports with real service banners

> 🚩 **Red flag:** Box has 500 open ports, all showing 'tcpwrapped'

---

### 200 OK on everything 🕐 `15-20 min`

**Service:** Web

**Why it's a trap:** Some servers return 200 for non-existent pages (custom 404). Gobuster will show thousands of 'hits'

**Do this instead:** Check response size. Filter: gobuster -fs &lt;404_size&gt;. Compare real pages vs non-existent pages first

> 🚩 **Red flag:** Every gobuster result is Status 200 with the same size

---

### login page with no known CMS 🕐 `20-30 min`

**Service:** Web

**Why it's a trap:** Custom login page doesn't mean SQLi. Could be rate-limited, properly coded, or irrelevant to the intended path

**Do this instead:** Try 3 things: default creds, admin'-- -, check source code. If nothing in 5 min, look for other entry points

> 🚩 **Red flag:** Spending 30 min on a login page that's not the intended path

---

### linPEAS shows 95% colors 🕐 `20-30 min`

**Service:** PrivEsc

**Why it's a trap:** linPEAS highlights tons of potential vectors. Most are informational, not exploitable. The tool is noisy by design

**Do this instead:** Focus on RED and YELLOW highlights. Check: sudo -l, SUID, writable cron, capabilities, docker/lxd groups FIRST before reading the entire output

> 🚩 **Red flag:** Reading every line of linPEAS output instead of checking the high-priority items first

---

### SUID /usr/bin/sudo 🕐 `5-10 min`

**Service:** PrivEsc

**Why it's a trap:** sudo having SUID is NORMAL. It's supposed to have SUID. Focus on non-standard SUID binaries

**Do this instead:** Ignore standard SUID: sudo, passwd, ping, mount, su, chsh, newgrp, gpasswd. Look for anything in /opt, /home, /usr/local, or custom paths

> 🚩 **Red flag:** Trying to exploit SUID on /usr/bin/sudo (it's meant to be SUID)

---

### Kernel 'vulnerable' but exploit fails 🕐 `30-45 min`

**Service:** PrivEsc

**Why it's a trap:** linux-exploit-suggester matches by version range but doesn't check if patches are applied via other means (backports, manual patches)

**Do this instead:** Check if the specific commit/fix is present. Try the exploit once cleanly. If it fails, it's likely patched despite the version number

> 🚩 **Red flag:** Third kernel exploit attempt has failed

---
