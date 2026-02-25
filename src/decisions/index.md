# 🌳 Decision Trees

When to move on, when to go back, and how to avoid wasting time.

---

## SERVICE PRIORITY ORDER - Enumerate in this order, not randomly

### HTTP/HTTPS (80/443/8080/8443) ⏱ `60 min max`

**Phase:** Tier 1
HIGH PRIORITY

✅ **If YES / Success:**

Web apps are the #1 foothold vector.
Check: source, dirs, params, login pages, CMS, known CVEs, file upload, SQLi, LFI

❌ **If NO / Fail:**

If nothing after 60 min: move to Tier 2.
Come back with new info (creds, usernames)

> ⚠️ **Bail Signal:** 60 min on web with nothing = move on.
You're probably missing a vhost, param, or need creds from another service

---

### SMB (445) ⏱ `20 min max`

**Phase:** Tier 1
HIGH PRIORITY

✅ **If YES / Success:**

Null sessions, share access, known vulns (EternalBlue), user enumeration.
Instant wins are common here

❌ **If NO / Fail:**

If no null session and no creds: move on.
Come back after finding creds on other services

> ⚠️ **Bail Signal:** No null session + no creds = nothing more to do here until you have creds

---

### Active Directory services (LDAP/Kerberos) ⏱ `30 min max`

**Phase:** Tier 1
HIGH PRIORITY

✅ **If YES / Success:**

Anonymous LDAP bind, AS-REP roasting (no creds needed), user enumeration.
Kerberoast once you have ANY cred

❌ **If NO / Fail:**

Need at least 1 credential to go further.
Get creds from other services first

> ⚠️ **Bail Signal:** No anonymous bind + no creds = come back later

---

### FTP (21) ⏱ `10 min max`

**Phase:** Tier 2
MEDIUM

✅ **If YES / Success:**

Anonymous login? YES = download everything.
Known version exploit? Check searchsploit

❌ **If NO / Fail:**

No anon + no exploit = skip entirely.
Come back only if you find creds

> ⚠️ **Bail Signal:** 10 minutes max. FTP is either instant win or nothing

---

### SSH (22) ⏱ `5 min max`

**Phase:** Tier 2
MEDIUM

✅ **If YES / Success:**

Banner grab for version. Try found creds.
NEVER brute force SSH without a good username

❌ **If NO / Fail:**

Note version, move on.
SSH is almost never the initial foothold

> ⚠️ **Bail Signal:** Don't brute force SSH as your first move. It's almost always a cred reuse entry

---

### MSSQL/MySQL (1433/3306) ⏱ `15 min max`

**Phase:** Tier 2
MEDIUM

✅ **If YES / Success:**

Default creds (sa:sa, root:root).
If access: enum databases, read files, xp_cmdshell

❌ **If NO / Fail:**

No creds = move on.
Come back with found credentials

> ⚠️ **Bail Signal:** No default creds = can't do anything without creds

---

### NFS (2049) ⏱ `10 min max`

**Phase:** Tier 2
MEDIUM

✅ **If YES / Success:**

showmount -&gt; mount shares -&gt; check for SSH keys, configs, no_root_squash

❌ **If NO / Fail:**

No exports or nothing readable = skip

> ⚠️ **Bail Signal:** Very quick to check. Either useful or not

---

### SNMP (161 UDP) ⏱ `10 min max`

**Phase:** Tier 3
LOW

✅ **If YES / Success:**

Community string brute -&gt; full walk.
Can reveal: users, processes, passwords in command lines

❌ **If NO / Fail:**

No valid community string = skip

> ⚠️ **Bail Signal:** Only useful if you get a valid community string

---

### SMTP (25) ⏱ `10 min max`

**Phase:** Tier 3
LOW

✅ **If YES / Success:**

User enumeration only.
Collect valid usernames for brute force on other services

❌ **If NO / Fail:**

If VRFY disabled = try RCPT TO.
If nothing works = skip

> ⚠️ **Bail Signal:** You're only here for usernames, not exploitation

---

### RDP (3389) ⏱ `5 min max`

**Phase:** Tier 3
LOW

✅ **If YES / Success:**

BlueKeep check. Try found creds.
PTH if you have NTLM hashes

❌ **If NO / Fail:**

Note it exists, move on.
RDP is an entry point, not a foothold

> ⚠️ **Bail Signal:** RDP = use after you have creds, not for initial access

---

## PHASE DECISION POINTS - When to advance, when to go back

### Ports found? ⏱ `15 min`

**Phase:** Scanning

✅ **If YES / Success:**

Enumerate services in priority order above

❌ **If NO / Fail:**

Re-scan: try different speed, try from VPN, check if host is up

> ⚠️ **Bail Signal:** If 2 scans show nothing = wrong target or host is down

---

### Found creds anywhere? ⏱ `45 min total`

**Phase:** Enumeration

✅ **If YES / Success:**

STOP enumeration. Spray creds on EVERY service immediately. This is your #1 priority

❌ **If NO / Fail:**

Keep enumerating. Check source code, config files, SNMP, NFS shares, default creds

> ⚠️ **Bail Signal:** 45 min with zero creds = you missed something. Go back to basics: source code, robots.txt, UDP

---

### Found a username but no password? ⏱ `15 min`

**Phase:** Enumeration

✅ **If YES / Success:**

Try: password = username, company name + year (Corp2024!), Season + year (Winter2024), basic combos (Password1, Welcome1)

❌ **If NO / Fail:**

AS-REP roast the user (no password needed). Check if account has Kerberos pre-auth disabled

> ⚠️ **Bail Signal:** Never brute force without a good username. Hydra on SSH with 'admin' is almost always a waste

---

### Found exploit for service version? ⏱ `20 min`

**Phase:** Enumeration

✅ **If YES / Success:**

Try the exploit. Read the code first. Check if it needs auth. Modify target IP/port

❌ **If NO / Fail:**

Is it the right version? Check exact version match. Try other exploits for similar versions

> ⚠️ **Bail Signal:** 20 min on one exploit = try a different approach. Read exploit code for clues about the vulnerability

---

### Found login page? ⏱ `20 min`

**Phase:** Web App

✅ **If YES / Success:**

1. Default creds (admin:admin, admin:password)
2. SQLi bypass (admin'-- -)
3. Check for registration
4. Brute force ONLY with known usernames

❌ **If NO / Fail:**

Look for other entry points: file upload, API endpoints, hidden params, different vhosts

> ⚠️ **Bail Signal:** 20 min on login with no creds = find creds elsewhere first. Don't blind brute force

---

### Found file upload? ⏱ `20 min`

**Phase:** Web App

✅ **If YES / Success:**

Try: PHP shell, extension bypasses (.phtml, .php5, double ext), magic bytes, .htaccess

❌ **If NO / Fail:**

If all blocked: look for other vectors. Upload might be a rabbit hole if heavily filtered

> ⚠️ **Bail Signal:** If 5+ bypass attempts fail = probably not the intended path

---

### Found LFI? ⏱ `30 min`

**Phase:** Web App

✅ **If YES / Success:**

1. Read /etc/passwd for users
2. Read config files for creds
3. Read SSH keys
4. Try log poisoning for RCE
5. Try PHP wrappers

❌ **If NO / Fail:**

Confirmed LFI but can't get RCE from it? Use it for information gathering (configs, users) and pivot to another vector

> ⚠️ **Bail Signal:** LFI without RCE is still valuable for reading files. Don't spend 2 hours trying log poisoning if it's not working

---

### Got shell but stuck on privesc? ⏱ `45 min`

**Phase:** Foothold

✅ **If YES / Success:**

Run linPEAS/winPEAS. Check: sudo -l, SUID, cron, capabilities, groups, config files, history, internal services

❌ **If NO / Fail:**

Transfer and run automated tools. Check for kernel exploits. Look for other users to pivot to

> ⚠️ **Bail Signal:** 45 min stuck = you missed something in enum. Re-read linPEAS output line by line. Check internal ports with netstat

---

### Got 1 domain credential? ⏱ `30 min`

**Phase:** AD

✅ **If YES / Success:**

Immediately: BloodHound, Kerberoast, check shares, spray on subnet, check admin access everywhere

❌ **If NO / Fail:**

AS-REP roast, LDAP anonymous bind, SNMP for users, password spray with common passwords

> ⚠️ **Bail Signal:** 1 domain cred unlocks massive attack surface. If stuck, re-run BloodHound queries

---

### Been stuck for 30+ min? ⏱ `Immediate`

**Phase:** Stuck

✅ **If YES / Success:**

ROTATE to a different machine or different service.
Fresh eyes fix 80% of 'stuck' situations.
Take a 5 min break, re-read your notes

❌ **If NO / Fail:**

If you've rotated through everything: re-enumerate from scratch. Something was missed in initial scan

> ⚠️ **Bail Signal:** The answer is almost ALWAYS in enumeration, not exploitation. Go back to enum

---

## OSCP EXAM TIME MANAGEMENT - 23h45m strategy

### Scan ALL machines ⏱ `1 hour`

**Phase:** Hour 0-1

✅ **If YES / Success:**

Run full TCP + UDP on every target simultaneously. While scans run: read exam guide, set up notes template, organize terminals

❌ **If NO / Fail:**

If scans haven't finished: start enumerating services that ARE showing up. Don't wait

> ⚠️ **Bail Signal:** Scan ALL boxes first before deep-diving any single one

---

### AD set: initial foothold ⏱ `2 hours`

**Phase:** Hour 1-3

✅ **If YES / Success:**

AD set is worth 40 points (most). Get initial foothold on AD. Enumerate domain once inside

❌ **If NO / Fail:**

If stuck after 2 hours: switch to standalone machines. Come back to AD with fresh eyes

> ⚠️ **Bail Signal:** AD requires 3 machines (foothold + privesc + DC). Get the first one and keep going

---

### Standalone machine 1 ⏱ `2 hours`

**Phase:** Hour 3-5

✅ **If YES / Success:**

Get foothold + privesc on easiest standalone. Take screenshots for report as you go

❌ **If NO / Fail:**

Stuck? Switch to other standalone. Mark where you got stuck in notes

> ⚠️ **Bail Signal:** 2 hours max per standalone. Rotate if stuck

---

### Standalone machine 2 + AD progress ⏱ `2 hours`

**Phase:** Hour 5-7

✅ **If YES / Success:**

Work second standalone or continue AD chain

❌ **If NO / Fail:**

Rotate between what's available

> ⚠️ **Bail Signal:** By hour 7 you should have ~40-50 points minimum

---

### Fill gaps ⏱ `Remaining`

**Phase:** Hour 7+

✅ **If YES / Success:**

Go back to machines where you got stuck. Try new approaches with info gathered from other boxes

❌ **If NO / Fail:**

If close to passing (70pts): focus on report quality not more flags

> ⚠️ **Bail Signal:** You need 70 points to pass. Calculate what you have and what you need

---

### Take breaks every 2 hours ⏱ `15 min each`

**Phase:** CRITICAL

✅ **If YES / Success:**

Step away from screen. Eat. Drink water. Walk around. Your brain processes problems in the background

❌ **If NO / Fail:**

Skipping breaks = diminishing returns after hour 4. You WILL miss things when tired

> ⚠️ **Bail Signal:** Breaks are not optional. They are strategic

---

### Screenshot EVERYTHING ⏱ `Ongoing`

**Phase:** CRITICAL

✅ **If YES / Success:**

For EVERY flag: whoami + ipconfig/ip a + flag content in same terminal screenshot

❌ **If NO / Fail:**

Missing screenshots = lost points even with correct flags

> ⚠️ **Bail Signal:** proof.txt + whoami + ifconfig in ONE screenshot

---
