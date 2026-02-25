# 📡 Recon & Scanning

Initial reconnaissance and port scanning methodology. Run these first on every target.

---

## NETWORK SCANNING

### Quick TCP scan (top 1000)

```bash
nmap -sC -sV -oN nmap/initial $IP
```

> Start here. Fast results.

**Why:** Find open ports quickly

<details>
<summary>Example Output</summary>

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1
80/tcp open  http    Apache 2.4.29
443/tcp open ssl/http Apache 2.4.29
```
</details>

---

### Full TCP port scan

```bash
nmap -p- -sC -sV -oN nmap/full $IP
```

> Run in background while enumerating initial results

**Why:** Don't miss high ports

<details>
<summary>Example Output</summary>

```
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
8443/tcp  open  https-alt
10000/tcp open  webmin
```
</details>

---

### UDP scan (top 20)

```bash
nmap -sU --top-ports 20 -oN nmap/udp $IP
```

> UDP is slow. Top 20 catches SNMP, TFTP, DNS

**Why:** SNMP and TFTP are common wins

<details>
<summary>Example Output</summary>

```
PORT    STATE SERVICE
53/udp  open  domain
161/udp open  snmp
69/udp  open  tftp
```
</details>

---

### Aggressive scan on specific ports

```bash
nmap -A -p 80,443,8080 -oN nmap/targeted $IP
```

> After finding ports, dig deeper

**Why:** OS detection, scripts, traceroute

<details>
<summary>Example Output</summary>

```
nmap -A -p 80,443 10.10.10.5
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache 2.4.29
|_http-title: My Website
OS: Linux 4.15
Traceroute: 1 hop
```
</details>

---

### Vulnerability scan

```bash
nmap --script vuln -p <ports> $IP
```

> Run against interesting services

**Why:** Quick CVE check

<details>
<summary>Example Output</summary>

```
| smb-vuln-ms17-010:
|   VULNERABLE:
|   Remote Code Execution
|   State: VULNERABLE
|   Risk factor: HIGH
```
</details>

---

### Rustscan (fast alternative)

```bash
rustscan -a $IP -- -sC -sV
```

> Scans all 65535 ports fast, passes to nmap

**Why:** Faster than nmap full scan

<details>
<summary>Example Output</summary>

```
rustscan -a 10.10.10.5 -- -sC -sV
Open 10.10.10.5:22
Open 10.10.10.5:80
Open 10.10.10.5:8443
(All 65535 ports scanned in 3 seconds)
```
</details>

---

## SERVICE VERSION RESEARCH

### Google each service + version

```bash
Search: "Apache 2.4.49 exploit"
```

> Include version numbers

**Why:** Known CVEs are free wins

<details>
<summary>Example Output</summary>

```
Search result: 'Apache 2.4.49 - Path Traversal & RCE (CVE-2021-41773)'
Exploit-DB: https://www.exploit-db.com/exploits/50383
```
</details>

---

### SearchSploit

```bash
searchsploit apache 2.4.49
```

> Check for public exploits

**Why:** Local exploit database

<details>
<summary>Example Output</summary>

```
Apache 2.4.49 - Path Traversal  | exploits/multiple/webapps/50383.sh
vsftpd 2.3.4 - Backdoor         | exploits/unix/remote/49757.py
```
</details>

---

### Check exploit-db.com

```bash
Browse exploit-db for service version
```

> More detail than searchsploit

**Why:** Proof of concepts available

<details>
<summary>Example Output</summary>

```
https://www.exploit-db.com/exploits/50383
Apache 2.4.49 - Path Traversal
Verified: Yes | Author: n/a
Type: webapps | Platform: Multiple
(Download exploit, read the code first)
```
</details>

---

### Check CVE databases

```bash
Google: site:nvd.nist.gov <service> <version>
```

> Official CVE details

**Why:** Severity scores, affected versions

<details>
<summary>Example Output</summary>

```
NVD: CVE-2021-41773
CVSS Score: 7.5 (HIGH)
Affected: Apache 2.4.49
Vector: Network/Low/None
(Confirms severity and affected versions)
```
</details>

---

### Check GitHub for PoCs

```bash
Search: "<CVE-XXXX-XXXX> exploit github"
```

> Often more reliable than exploit-db

**Why:** Working PoCs with instructions

<details>
<summary>Example Output</summary>

```
github.com search: CVE-2021-41773
Result: 47 repositories
Top: nuclei-templates/cves/2021/CVE-2021-41773.yaml
(Working PoC with usage instructions)
```
</details>

---
