# ONE-LINER CHAINS - Copy-paste enum scripts (replace $IP)

### Initial recon chain `~8 min for full picture`

**Category:** Full auto-enum

```bash
export IP=10.10.10.5 && mkdir -p loot && rustscan -a $IP -- -sC -sV -oN loot/tcp.txt & nmap -sU --top-ports 20 $IP -oN loot/udp.txt & whatweb http://$IP | tee loot/whatweb.txt
```

> Kick off everything at once. Review tcp.txt output first

<details>
<summary>Example Output</summary>

```
Creates loot/ directory
All 3 scans run in parallel
Check tcp.txt first when done
```
</details>

---

### Web recon chain `~3 min vs manual`

**Category:** Web auto-enum

```bash
export URL=http://10.10.10.5 && curl -s $URL/robots.txt; curl -s $URL/sitemap.xml; curl -sI $URL | grep -i 'server\|x-powered\|set-cookie'; gobuster dir -u $URL -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,txt -t 50 -q -o loot/dirs.txt &
```

> Gets robots, sitemap, headers, and starts gobuster in one paste

<details>
<summary>Example Output</summary>

```
robots.txt: /admin/ /backup/
Server: Apache/2.4.29
X-Powered-By: PHP/7.2
(gobuster running in background)
```
</details>

---

### SMB full chain `~2 min vs manual`

**Category:** SMB auto-enum

```bash
export IP=10.10.10.5 && echo '=== NULL SESSION ===' && smbclient -L //$IP -N 2>/dev/null && echo '=== ENUM4LINUX ===' && enum4linux -a $IP 2>/dev/null | grep -E 'user:|share:|password|group' && echo '=== SHARES ===' && smbmap -H $IP
```

> One paste gets null session, users, shares, and permissions

<details>
<summary>Example Output</summary>

```
=== NULL SESSION ===
Backups, Users, ADMIN$
=== ENUM4LINUX ===
user:[john] user:[admin]
=== SHARES ===
Backups READ
```
</details>

---

### Spray creds everywhere `~1 min vs manual per-service`

**Category:** Cred spray

```bash
export IP=10.10.10.5 U=admin P=Password1 && crackmapexec smb $IP -u $U -p $P && crackmapexec winrm $IP -u $U -p $P && crackmapexec ssh $IP -u $U -p $P && crackmapexec mssql $IP -u $U -p $P && crackmapexec ftp $IP -u $U -p $P 2>/dev/null
```

> Change U and P vars, paste once to test everywhere

<details>
<summary>Example Output</summary>

```
SMB  [+] admin:Password1
WINRM [+] admin:Password1 (Pwn3d!)
SSH  [-] admin:Password1
(WinRM works! Use evil-winrm)
```
</details>

---

### Linux enum chain `~30 sec vs 5 min manual`

**Category:** Linux privesc

```bash
echo '=== SUDO ===' && sudo -l 2>/dev/null; echo '=== SUID ===' && find / -perm -4000 -type f 2>/dev/null; echo '=== CRON ===' && cat /etc/crontab 2>/dev/null; ls -la /etc/cron.* 2>/dev/null; echo '=== CAPS ===' && getcap -r / 2>/dev/null; echo '=== WRITABLE ===' && find / -writable -type f -not -path '/proc/*' -not -path '/sys/*' 2>/dev/null | head -20
```

> Run before linPEAS for quick manual check. Covers the top 5 privesc vectors in seconds

<details>
<summary>Example Output</summary>

```
=== SUDO ===
(root) NOPASSWD: /usr/bin/vim
=== SUID ===
/usr/bin/pkexec
(Stop here - you have sudo vim!)
```
</details>

---

### Windows enum chain `~30 sec`

**Category:** Windows privesc

```bash
whoami /priv & whoami /groups & systeminfo | findstr /B /C:"OS" /C:"System" /C:"Hotfix" & netstat -ano | findstr LISTEN & cmdkey /list & reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated 2>nul
```

> Quick manual check before winPEAS. Catches SeImpersonate, saved creds, AlwaysInstallElevated

<details>
<summary>Example Output</summary>

```
SeImpersonatePrivilege Enabled
-> Immediate potato attack
(Skip winPEAS, go straight to PrintSpoofer)
```
</details>

---

### AD initial chain `~1 min`

**Category:** AD quick wins

```bash
echo '=== DOMAIN ===' && echo %userdomain% && echo '=== DC ===' && nltest /dclist:%userdomain% && echo '=== USERS ===' && net user /domain && echo '=== ADMINS ===' && net group 'Domain Admins' /domain && echo '=== POLICY ===' && net accounts /domain
```

> First things to run with any domain credentials

<details>
<summary>Example Output</summary>

```
Domain: CORP
DC: DC01.corp.local
Domain Admins: administrator, john.smith
Lockout threshold: 5
```
</details>

---

### PTH spray entire subnet `~2 min vs per-host`

**Category:** Hash spray subnet

```bash
crackmapexec smb 10.10.10.0/24 -u administrator -H $HASH --continue-on-success | grep 'Pwn3d'
```

> After getting admin hash, immediately check entire subnet

<details>
<summary>Example Output</summary>

```
10.10.10.5  DC01  (Pwn3d!)
10.10.10.20 WEB01 (Pwn3d!)
10.10.10.21 DB01  (Pwn3d!)
```
</details>

---
