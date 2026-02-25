# 📡 Recon & Scanning

Initial reconnaissance and port scanning methodology. Run these first on every target.

---

## NETWORK SCANNING

### Quick TCP scan (top 1000)

Start here. Fast results. — Find open ports quickly

```bash
nmap -sC -sV -oN nmap/initial $IP
```

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

Run in background while enumerating initial results — Don't miss high ports

```bash
nmap -p- -sC -sV -oN nmap/full $IP
```

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

UDP is slow. Top 20 catches SNMP, TFTP, DNS — SNMP and TFTP are common wins

```bash
nmap -sU --top-ports 20 -oN nmap/udp $IP
```

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

After finding ports, dig deeper — OS detection, scripts, traceroute

```bash
nmap -A -p 80,443,8080 -oN nmap/targeted $IP
```

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

Run against interesting services — Quick CVE check

```bash
nmap --script vuln -p <ports> $IP
```

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

Scans all 65535 ports fast, passes to nmap — Faster than nmap full scan

```bash
rustscan -a $IP -- -sC -sV
```

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

Include version numbers — Known CVEs are free wins

```bash
Search: "Apache 2.4.49 exploit"
```

<details>
<summary>Example Output</summary>

```
Search result: 'Apache 2.4.49 - Path Traversal & RCE (CVE-2021-41773)'
Exploit-DB: https://www.exploit-db.com/exploits/50383
```
</details>

---

### SearchSploit

Check for public exploits — Local exploit database

```bash
searchsploit apache 2.4.49
```

<details>
<summary>Example Output</summary>

```
Apache 2.4.49 - Path Traversal  | exploits/multiple/webapps/50383.sh
vsftpd 2.3.4 - Backdoor         | exploits/unix/remote/49757.py
```
</details>

---

### Check exploit-db.com

More detail than searchsploit — Proof of concepts available

```bash
Browse exploit-db for service version
```

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

Official CVE details — Severity scores, affected versions

```bash
Google: site:nvd.nist.gov <service> <version>
```

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

Often more reliable than exploit-db — Working PoCs with instructions

```bash
Search: "<CVE-XXXX-XXXX> exploit github"
```

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
