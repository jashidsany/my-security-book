# Methodology Flowchart

Your master decision tree for engagements. When you're stuck, come back here.

---

## PHASE 1: RECONNAISSANCE

```
┌─────────────────────────────────────┐
│         START: NEW TARGET           │
│            nmap -sC -sV -p- $IP     │
└──────────────┬──────────────────────┘
               │
               ▼
┌─────────────────────────────────────┐
│     WHAT PORTS ARE OPEN?            │
│     Organize by service type        │
└──────────────┬──────────────────────┘
               │
       ┌───────┼───────┬──────────┬──────────┐
       ▼       ▼       ▼          ▼          ▼
     Web    SMB/RPC   FTP      SSH/RDP    Database
   80/443  139/445    21       22/3389   3306/1433/5432
```

---

## PHASE 2: PORT → ACTION MAP

### I see a web port (80, 443, 8080, 8443)

```
Web Port Found
  │
  ├─→ Browse manually first (check source, robots.txt, comments)
  ├─→ whatweb / wappalyzer → Identify technology
  ├─→ gobuster dir → Find hidden directories
  ├─→ gobuster vhost → Find virtual hosts (if domain found)
  │
  ├─→ Is it a known app?
  │     ├─ WordPress  → wpscan, theme/plugin RCE
  │     ├─ Tomcat     → /manager default creds, WAR deploy
  │     ├─ Jenkins    → /script Groovy console
  │     ├─ Drupal     → Drupalgeddon2/3
  │     ├─ Joomla     → joomscan, template edit
  │     ├─ Grafana    → LFI CVE-2021-43798
  │     ├─ Webmin     → CVE-2019-15107
  │     ├─ GitLab     → CVE-2021-22205, explore repos
  │     ├─ phpMyAdmin → default creds, INTO OUTFILE
  │     ├─ Flask      → Werkzeug debugger, SSTI
  │     └─ CMS Made Simple → SQLi CVE-2019-9053
  │
  ├─→ Is there a login page?
  │     ├─ Try default creds (admin:admin, admin:password)
  │     ├─ Try found usernames + common passwords
  │     ├─ Check for SQLi in login form
  │     └─ Brute force with hydra (last resort)
  │
  └─→ Test for vulns:
        ├─ LFI → /etc/passwd, log poisoning
        ├─ RFI → Host PHP shell
        ├─ SQLi → Manual UNION, error-based, blind
        ├─ Command injection → ; | ` $() 
        ├─ File upload → Bypass extension filters
        ├─ SSTI → {{7*7}} in input fields
        └─ XXE → XML input points
```

---

### I see FTP (21)

```
FTP Port Found
  │
  ├─→ Check version (nmap -sV)
  │     ├─ vsftpd 2.3.4 → Backdoor! (port 6200)
  │     └─ ProFTPD 1.3.5 → mod_copy (CPFR/CPTO)
  │
  ├─→ Try anonymous login: ftp $IP → anonymous / (no pass)
  │     ├─ YES → Download everything, check for creds/configs
  │     └─ NO  → Move on, try creds later
  │
  └─→ Can you upload? → Upload web shell if FTP root = web root
```

---

### I see SSH (22)

```
SSH Port Found
  │
  ├─→ Do you have credentials? → Try them
  ├─→ Do you have a username? → Hydra with targeted wordlist
  ├─→ Do you have a private key? → chmod 600 + ssh -i
  ├─→ Old version? → Check searchsploit
  └─→ Otherwise → Come back after finding creds elsewhere
  
  ⚠️ Don't waste time brute forcing SSH without a username
```

---

### I see SMB (139/445)

```
SMB Port Found
  │
  ├─→ enum4linux -a $IP
  ├─→ smbclient -L //$IP/ -N (list shares)
  ├─→ smbmap -H $IP (check permissions)
  ├─→ crackmapexec smb $IP -u '' -p '' --shares
  │
  ├─→ Readable shares? → Download everything, grep for creds
  ├─→ Writable shares? → Upload SCF/LNK for hash capture
  ├─→ Found usernames? → Try password spraying
  │
  ├─→ Check Samba version:
  │     └─ Samba 3.5-4.6 → SambaCry CVE-2017-7494
  │
  └─→ With creds:
        ├─ crackmapexec smb $IP -u user -p pass --shares
        ├─ psexec / wmiexec / smbexec for shell
        └─ secretsdump for hash extraction
```

---

### I see DNS (53)

```
DNS Port Found
  │
  ├─→ Try zone transfer: dig axfr @$IP domain.com
  ├─→ Reverse lookup: dig -x $IP @$IP
  ├─→ Brute force subdomains: gobuster dns -d domain.com -w wordlist
  └─→ Add discovered hostnames to /etc/hosts
```

---

### I see SMTP (25/465/587)

```
SMTP Port Found
  │
  ├─→ User enumeration: smtp-user-enum -M VRFY -U users.txt -t $IP
  ├─→ Check version → Exim 4.87-4.91? → RCE CVE-2019-10149
  └─→ Send phishing email (if client-side attacks in scope)
```

---

### I see SNMP (161 UDP)

```
SNMP Port Found
  │
  ├─→ snmpwalk -v2c -c public $IP
  ├─→ onesixtyone -c community.txt $IP
  ├─→ snmpwalk -v2c -c public $IP NET-SNMP-EXTEND-MIB::nsExtendObjects
  └─→ Look for: running processes, installed software, usernames, passwords
```

---

### I see Database (3306/1433/5432)

```
Database Port Found
  │
  ├─→ MySQL (3306)
  │     ├─ mysql -h $IP -u root -p
  │     ├─ Try: root:(empty), root:root, root:password
  │     └─ UDF exploitation for RCE
  │
  ├─→ MSSQL (1433)
  │     ├─ impacket-mssqlclient user:pass@$IP
  │     ├─ xp_cmdshell for RCE
  │     └─ Try: sa:sa, sa:(empty)
  │
  └─→ PostgreSQL (5432)
        ├─ psql -h $IP -U postgres
        ├─ COPY TO PROGRAM for RCE
        └─ Try: postgres:postgres
```

---

### I see RDP (3389)

```
RDP Port Found
  │
  ├─→ Do you have creds? → xfreerdp /u:user /p:pass /v:$IP
  ├─→ Try: administrator:(found passwords)
  ├─→ Brute force: hydra -l admin -P wordlist.txt $IP rdp
  └─→ BlueKeep (CVE-2019-0708)? → nmap --script rdp-vuln-ms12-020
```

---

### I see LDAP (389/636)

```
LDAP Port Found
  │
  ├─→ ldapsearch -x -H ldap://$IP -b "" -s base namingContexts
  ├─→ ldapsearch -x -H ldap://$IP -b "DC=domain,DC=local" 
  ├─→ Enumerate users, groups, computers
  └─→ Found domain? → AD enumeration path
```

---

### I see NFS (2049)

```
NFS Port Found
  │
  ├─→ showmount -e $IP
  ├─→ Mount shares: mount -t nfs $IP:/share /mnt
  ├─→ Check for sensitive files (SSH keys, configs)
  └─→ no_root_squash? → Create SUID binary for privesc
```

---

### I see WinRM (5985/5986)

```
WinRM Port Found
  │
  ├─→ Need valid credentials
  ├─→ evil-winrm -i $IP -u user -p 'password'
  └─→ User must be in "Remote Management Users" group
```

---

### I see Redis (6379)

```
Redis Port Found
  │
  ├─→ redis-cli -h $IP → Try no auth
  ├─→ INFO, CONFIG GET dir, CONFIG GET dbfilename
  ├─→ Write SSH key: redis-cli set payload "ssh-rsa ..."
  └─→ Write web shell if web root known
```

---

## PHASE 3: GOT A SHELL — NOW WHAT?

```
Got Initial Shell
  │
  ├─→ IMMEDIATELY:
  │     ├─ whoami / id
  │     ├─ hostname
  │     ├─ ip addr / ifconfig (look for dual-homed)
  │     └─ Upgrade shell: python3 -c 'import pty;pty.spawn("/bin/bash")'
  │
  ├─→ LINUX:
  │     ├─ sudo -l (FIRST THING — check GTFOBins)
  │     ├─ find / -perm -4000 2>/dev/null (SUID)
  │     ├─ cat /etc/crontab + ls -la /etc/cron*
  │     ├─ LinPEAS / linpeas.sh
  │     ├─ Check: /etc/passwd, /etc/shadow (readable?)
  │     ├─ Check: /home/*/.bash_history, /home/*/.ssh/
  │     ├─ Check: env vars, config files, DB connections
  │     ├─ ps aux → running services as root?
  │     ├─ netstat -tulnp → internal services?
  │     └─ Kernel version → searchsploit
  │
  └─→ WINDOWS:
        ├─ whoami /priv (SeImpersonate? → Potato attacks)
        ├─ whoami /groups
        ├─ systeminfo → OS version, patches
        ├─ WinPEAS / winpeas.exe
        ├─ Check: stored creds, autologon, SAM backups
        ├─ reg query for passwords
        ├─ Check services: sc query, accesschk
        ├─ Scheduled tasks: schtasks /query
        ├─ netstat -ano → internal services?
        └─ PowerUp.ps1 / Seatbelt.exe
```

---

## PHASE 4: ACTIVE DIRECTORY PATH

```
Found AD Environment
  │
  ├─→ FROM FOOTHOLD:
  │     ├─ BloodHound (SharpHound.exe -c All)
  │     ├─ Enumerate users: net user /domain
  │     ├─ Enumerate groups: net group /domain
  │     ├─ Find SPNs: setspn -L on interesting accounts
  │     └─ Check: who am I in AD? What groups?
  │
  ├─→ QUICK WINS:
  │     ├─ Kerberoast → hashcat -m 13100
  │     ├─ AS-REP Roast → hashcat -m 18200
  │     ├─ Password spray: Season+Year! (Summer2025!)
  │     └─ Responder → capture NTLMv2 → crack or relay
  │
  ├─→ LATERAL MOVEMENT:
  │     ├─ Pass the Hash (crackmapexec, psexec)
  │     ├─ Pass the Ticket (Rubeus)
  │     ├─ WinRM with found creds
  │     └─ RDP with found creds
  │
  ├─→ ESCALATION TO DA:
  │     ├─ BloodHound → "Shortest Path to DA"
  │     ├─ Delegation attacks (unconstrained/constrained/RBCD)
  │     ├─ PrintNightmare
  │     ├─ ACL abuse (GenericAll, WriteDACL, etc.)
  │     └─ DCSync (if you have the rights)
  │
  └─→ POST-DA:
        ├─ secretsdump → dump all hashes
        ├─ Golden Ticket (krbtgt hash)
        ├─ Loot: SYSVOL, GPP passwords, LAPS
        └─ Proof: screenshot of DA + flag
```

---

## PHASE 5: STUCK? RESET CHECKLIST

```
I'm Stuck
  │
  ├─→ Did I scan ALL ports? (nmap -p- not just top 1000)
  ├─→ Did I scan UDP? (nmap -sU --top-ports 100)
  ├─→ Did I check every version for exploits? (searchsploit)
  ├─→ Did I try ALL found creds on ALL services?
  ├─→ Did I check for virtual hosts / subdomains?
  ├─→ Did I read source code / comments carefully?
  ├─→ Did I check robots.txt, .git, .env, backup files?
  ├─→ Did I try default creds on every login?
  ├─→ Did I look at EVERYTHING on accessible shares?
  ├─→ Am I looking at an internal service? (port forward it)
  │
  └─→ Still stuck after all this?
        ├─ Take a 10-minute break
        ├─ Re-read your notes from scratch
        └─ Move to another machine and come back
```

---

## EXAM TIME MANAGEMENT

```
23h 45m Exam Timer
  │
  ├─→ FIRST 30 MIN:
  │     Full nmap scan on ALL machines simultaneously
  │     Read all results, plan attack order
  │
  ├─→ HOURS 1-8: AD SET (40 points)
  │     ├─ Must get all 3 machines in the chain
  │     ├─ Start with enumeration → foothold → privesc → lateral → DA
  │     └─ If stuck after 3 hours on one step → move to standalones
  │
  ├─→ HOURS 8-16: STANDALONE MACHINES (20 pts each)
  │     ├─ Pick the easiest-looking one first
  │     ├─ 2-3 hours per machine MAX
  │     └─ Low-priv shell = 10 pts, root = 20 pts
  │
  ├─→ HOURS 16-20: RETURN TO STUCK MACHINES
  │     └─ Fresh eyes often find what you missed
  │
  ├─→ LAST 3 HOURS:
  │     ├─ SCREENSHOTS of every proof.txt / local.txt
  │     ├─ Document exact steps to reproduce
  │     └─ Start writing report notes
  │
  └─→ SCREENSHOT CHECKLIST:
        ├─ ip addr / ipconfig showing target IP
        ├─ whoami showing root/SYSTEM/DA
        ├─ cat proof.txt / type proof.txt
        └─ All in ONE screenshot together
```

---

*When in doubt: enumerate harder, spray creds wider, and check internal ports.*
