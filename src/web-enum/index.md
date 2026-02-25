# 🌐 Web Enumeration

Web application reconnaissance and content discovery.

---

## INITIAL WEB RECON

### Browse manually in browser

```bash
http://$IP
https://$IP
http://$IP:8080
```

> Look at the site. Read it. Click around.

**Why:** Context before tools

<details>
<summary>Example Output</summary>

```
See a login page, CMS, or custom app
Check footer: 'Powered by WordPress 5.8'
Check URLs: /index.php?page=about (LFI candidate)
```
</details>

---

### Check page source (Ctrl+U)

```bash
View source on every page
```

> Comments, hidden fields, JS files, API endpoints

**Why:** Devs leave secrets in comments

<details>
<summary>Example Output</summary>

```
<!-- TODO: remove debug credentials -->
<!-- admin:Passw0rd123 -->
<script src='/api/v1/config.js'></script>
<input type='hidden' name='debug' value='true'>
```
</details>

---

### Check robots.txt

```bash
curl http://$IP/robots.txt
```

> Disallowed paths

**Why:** Paths they don't want indexed

<details>
<summary>Example Output</summary>

```
User-agent: *
Disallow: /admin/
Disallow: /backup/
Disallow: /internal/
Disallow: /wp-admin/
(Now you know hidden paths exist)
```
</details>

---

### Check sitemap.xml

```bash
curl http://$IP/sitemap.xml
```

> Site structure

**Why:** Hidden pages and directories

<details>
<summary>Example Output</summary>

```
curl http://10.10.10.5/sitemap.xml
<urlset>
  <url><loc>/internal-dashboard/</loc></url>
  <url><loc>/api/v2/users/</loc></url>
  <url><loc>/old-admin/</loc></url>
</urlset>
(Hidden paths revealed)
```
</details>

---

### Check security.txt

```bash
curl http://$IP/.well-known/security.txt
```

> Security contact info

**Why:** May reveal tech stack

<details>
<summary>Example Output</summary>

```
curl http://10.10.10.5/.well-known/security.txt
Contact: security@corp.local
Preferred-Languages: en
Canonical: https://corp.local/.well-known/security.txt
(Confirms domain, may reveal email format)
```
</details>

---

### Identify technology stack

```bash
whatweb http://$IP
wappalyzer (browser extension)
```

> CMS, framework, language, server

**Why:** Guides your attack approach

<details>
<summary>Example Output</summary>

```
whatweb http://10.10.10.5
http://10.10.10.5 [200 OK]
  Apache[2.4.29], PHP[7.2.10],
  WordPress[5.8], jQuery[3.6.0],
  Country[US]
```
</details>

---

### Check HTTP headers

```bash
curl -I http://$IP
```

> Server, X-Powered-By, cookies

**Why:** Version info, security headers

<details>
<summary>Example Output</summary>

```
HTTP/1.1 200 OK
Server: Apache/2.4.29 (Ubuntu)
X-Powered-By: PHP/7.2.10
Set-Cookie: PHPSESSID=abc123
(Versions exposed = search for CVEs)
```
</details>

---

### Check SSL/TLS cert

```bash
openssl s_client -connect $IP:443
```

> Certificate details

**Why:** Hostnames, org info, valid dates

<details>
<summary>Example Output</summary>

```
openssl s_client -connect 10.10.10.5:443
subject=CN = dev.corp.local
subjectAltName = DNS:dev.corp.local, DNS:staging.corp.local
(Found additional hostnames to add to /etc/hosts)
```
</details>

---

### Add hostname to /etc/hosts

```bash
echo '$IP hostname.htb' >> /etc/hosts
```

> If you discover a hostname

**Why:** Virtual host routing

<details>
<summary>Example Output</summary>

```
echo '10.10.10.5 target.htb dev.target.htb' >> /etc/hosts

curl http://target.htb -> 'Welcome to Our App'
curl http://10.10.10.5 -> 'Apache Default Page'
(Different content = virtual hosting confirmed)
```
</details>

---

### Screenshot all web ports

```bash
gowitness single http://$IP
```

> Visual record

**Why:** Quick reference for reporting

<details>
<summary>Example Output</summary>

```
gowitness single http://10.10.10.5
gowitness single https://10.10.10.5
gowitness single http://10.10.10.5:8080
(Visual screenshots saved for reference and reporting)
```
</details>

---

## DIRECTORY / FILE DISCOVERY

### Gobuster directory scan

```bash
gobuster dir -u http://$IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -o gobuster.txt
```

> Main directory brute force

**Why:** Find hidden directories

<details>
<summary>Example Output</summary>

```
=======================================
/admin        (Status: 301)
/uploads      (Status: 301)
/backup       (Status: 301)
/config       (Status: 403)
/phpmyadmin   (Status: 200)
/api          (Status: 200)
```
</details>

---

### Gobuster with extensions

```bash
gobuster dir -u http://$IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,txt,html,bak,old,conf,zip -o gobuster_ext.txt
```

> Find files with extensions

**Why:** php/txt/bak files are gold

<details>
<summary>Example Output</summary>

```
/config.php.bak  (Status: 200)  <- backup config!
/notes.txt       (Status: 200)  <- dev notes
/shell.php       (Status: 200)  <- existing shell?
/database.sql    (Status: 200)  <- DB dump
```
</details>

---

### Feroxbuster (recursive)

```bash
feroxbuster -u http://$IP -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
```

> Recursive scanning

**Why:** Finds nested directories

<details>
<summary>Example Output</summary>

```
feroxbuster -u http://10.10.10.5
200  GET  /admin/
200  GET  /admin/config/
200  GET  /admin/config/database.yml  <- nested find!
301  GET  /backup/
200  GET  /backup/site_2024.zip
```
</details>

---

### Scan discovered directories

```bash
gobuster dir -u http://$IP/<found_dir> -w <wordlist> -x php,txt
```

> Enumerate inside each dir found

**Why:** Go deeper

<details>
<summary>Example Output</summary>

```
gobuster dir -u http://10.10.10.5/admin -w wordlist.txt -x php
/admin/login.php    (Status: 200)
/admin/config.php   (Status: 200)
/admin/uploads/     (Status: 301)
/admin/backup.php   (Status: 200)
```
</details>

---

### Virtual host / subdomain scan

```bash
gobuster vhost -u http://<domain> -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

> Find virtual hosts

**Why:** Different sites on same server

<details>
<summary>Example Output</summary>

```
Found: dev.domain.htb (Status: 200) [Size: 5432]
Found: staging.domain.htb (Status: 200) [Size: 1234]
Found: admin.domain.htb (Status: 302) [Size: 0]
(Add each to /etc/hosts and browse)
```
</details>

---

### FFUF for fuzzing

```bash
ffuf -u http://$IP/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words.txt -mc 200,301,302
```

> Fast fuzzer

**Why:** Flexible filtering

<details>
<summary>Example Output</summary>

```
ffuf -u http://10.10.10.5/FUZZ -w wordlist.txt -mc 200,301
admin          [Status: 301, Size: 312]
backup         [Status: 301, Size: 314]
api            [Status: 200, Size: 1543]
.env           [Status: 200, Size: 256]
```
</details>

---

### Check for common files

```bash
curl http://$IP/wp-config.php.bak
curl http://$IP/.git/HEAD
curl http://$IP/.env
curl http://$IP/web.config
curl http://$IP/config.php
```

> Manual checks for sensitive files

**Why:** Backup configs, git repos, env files

<details>
<summary>Example Output</summary>

```
curl http://10.10.10.5/.git/HEAD
ref: refs/heads/master
(GIT REPO EXPOSED! Use git-dumper)

curl http://10.10.10.5/.env
DB_PASSWORD=s3cretDBp@ss
APP_KEY=base64:abc123...
```
</details>

---

### Nikto web scanner

```bash
nikto -h http://$IP
```

> Vulnerability scanner

**Why:** Quick scan for known issues

<details>
<summary>Example Output</summary>

```
+ Server: Apache/2.4.29 (Ubuntu)
+ /: The X-Content-Type-Options header is not set
+ /config.php: PHP Config file found
+ /backup/: Directory indexing found
+ OSVDB-3233: /icons/README: Apache default file found
```
</details>

---

## CMS DETECTION & ENUMERATION

### WordPress scan

```bash
wpscan --url http://$IP --enumerate ap,at,u --api-token <token>
```

> Plugins, themes, users

**Why:** WP is full of vuln plugins

<details>
<summary>Example Output</summary>

```
wpscan output:
[+] WordPress version 5.8 identified
[!] 3 vulnerabilities identified
[+] Plugin: wp-file-manager 6.9
  | [!] CVE-2020-25213 - Unauthenticated RCE
[+] Users found: admin, editor
```
</details>

---

### WordPress user enum

```bash
wpscan --url http://$IP --enumerate u
```

> Find usernames

**Why:** Then brute force wp-login

<details>
<summary>Example Output</summary>

```
wpscan --url http://10.10.10.5 --enumerate u
[+] admin
 | Found By: Author Posts
[+] editor
 | Found By: Wp Json Api
(Now brute force these usernames)
```
</details>

---

### WordPress brute force

```bash
wpscan --url http://$IP --usernames <user> --passwords /usr/share/wordlists/rockyou.txt
```

> Login brute force

**Why:** Weak admin passwords

<details>
<summary>Example Output</summary>

```
wpscan --url http://10.10.10.5 --usernames admin --passwords rockyou.txt
[+] Performing password attack on Xmlrpc against 1 user(s)
[SUCCESS] - admin / sunshine1
(Login at /wp-admin with admin:sunshine1)
```
</details>

---

### Joomla scan

```bash
joomscan -u http://$IP
```

> Joomla enumeration

**Why:** Version, plugins, vulns

<details>
<summary>Example Output</summary>

```
[+] Joomla 3.7.0
[++] Joomla 3.7.0 - SQL Injection
    CVE: CVE-2017-8917
    URL: /index.php?option=com_fields&view=fields&layout=modal
```
</details>

---

### Drupal scan

```bash
droopescan scan drupal -u http://$IP
```

> Drupal enumeration

**Why:** Drupalgeddon exploits

<details>
<summary>Example Output</summary>

```
droopescan scan drupal -u http://10.10.10.5
[+] Version: 7.54
[+] Plugins: php_filter, views
[!] Drupal < 7.58 - Drupalgeddon2 (CVE-2018-7600)
(Unauthenticated RCE if vulnerable)
```
</details>

---

### Check /wp-admin, /administrator, /admin

```bash
Browse to common admin paths
```

> Manual CMS detection

**Why:** Find login panels

<details>
<summary>Example Output</summary>

```
http://10.10.10.5/wp-admin -> WordPress login
http://10.10.10.5/administrator -> Joomla login
http://10.10.10.5/admin -> Custom admin panel
(Found login page = try defaults, SQLi, brute force)
```
</details>

---

### Check CMS version

```bash
View source for generator meta tag
curl http://$IP/readme.html
curl http://$IP/CHANGELOG.txt
```

> Exact version for exploit search

**Why:** Version-specific exploits

<details>
<summary>Example Output</summary>

```
View source: <meta name='generator' content='WordPress 5.8'/>
curl http://10.10.10.5/readme.html -> WordPress 5.8
curl http://10.10.10.5/CHANGELOG.txt -> Drupal 7.54
(Exact version for exploit search)
```
</details>

---
