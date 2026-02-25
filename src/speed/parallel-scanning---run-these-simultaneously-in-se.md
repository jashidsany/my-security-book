# PARALLEL SCANNING - Run these simultaneously in separate terminals

### Rustscan all TCP ports `~10 min vs nmap full`

**Category:** Phase 0
(Immediate)

```bash
rustscan -a $IP -- -sC -sV -oN rustscan.txt
```

> Scans all 65535 in seconds, then runs nmap scripts on open ports

<details>
<summary>Example Output</summary>

```
Open 10.10.10.5:22,80,445,8080
nmap scan on 4 ports...
```
</details>

---

### Quick nmap while rustscan runs `Redundancy`

**Category:** Phase 0
(Immediate)

```bash
nmap -sC -sV -p- --min-rate 5000 $IP -oN full_tcp.txt
```

> Backup scan in case rustscan misses filtered ports

<details>
<summary>Example Output</summary>

```
Use --min-rate 5000 for speed
Still catches filtered ports rustscan may miss
```
</details>

---

### UDP top 20 (background) `Runs while you work`

**Category:** Phase 0
(Immediate)

```bash
nmap -sU --top-ports 20 --min-rate 5000 $IP -oN udp.txt &
```

> Run in background with &, check later. SNMP/TFTP/DNS are common wins

<details>
<summary>Example Output</summary>

```
53/udp open  domain
161/udp open  snmp
(Check these immediately)
```
</details>

---

### Parallel service enum `50% faster overall`

**Category:** Phase 1
(After ports)

```bash
# Terminal 1: web enum
gobuster dir -u http://$IP -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,txt,html -t 50 -o dirs.txt

# Terminal 2: SMB enum
enum4linux -a $IP | tee smb.txt

# Terminal 3: check exploits
searchsploit <service versions>
```

> Never enumerate one service at a time. Always have 2-3 terminals running different services

<details>
<summary>Example Output</summary>

```
While gobuster runs:
- Read SMB shares
- Research CVEs
- Check other ports
```
</details>

---

### Auto-scope gobuster to tech stack `~5 min per scan`

**Category:** Phase 1

```bash
# PHP site:
gobuster dir -x php,php.bak,txt,html,conf

# ASP/IIS site:
gobuster dir -x asp,aspx,config,txt,html

# Java site:
gobuster dir -x jsp,do,action,xml,properties
```

> Wrong extensions = wasted scan time. Check tech stack FIRST with whatweb or headers

<details>
<summary>Example Output</summary>

```
whatweb says PHP/Apache:
Only use php,txt,html extensions
Don't waste time on asp,aspx
```
</details>

---

### Immediate credential check `Prevents hours of unnecessary enum`

**Category:** Phase 1

```bash
# Run against ALL services with found creds
crackmapexec smb $IP -u found_user -p found_pass
crackmapexec winrm $IP -u found_user -p found_pass
crackmapexec ssh $IP -u found_user -p found_pass
smbclient -L //$IP -U found_user%found_pass
```

> The SECOND you find any credential, spray it everywhere before doing anything else

<details>
<summary>Example Output</summary>

```
Found admin:admin on FTP
Try on SSH -> WORKS!
(Skip the web app entirely)
```
</details>

---
