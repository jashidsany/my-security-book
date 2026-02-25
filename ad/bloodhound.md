# BloodHound

### Collect data with SharpHound

Run on domain-joined machine — Generates ZIP for BloodHound

```bash
.\SharpHound.exe -c All
# Or PowerShell:
Import-Module .\SharpHound.ps1
Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\Temp
```

---

### Collect with BloodHound.py (from Linux)

Remote collection without touching target — Uses LDAP

```bash
bloodhound-python -u user -p 'password' -d domain.local -ns $DC_IP -c all
```

---

### Start BloodHound

Launch Neo4j and BloodHound GUI — Import collected data

```bash
sudo neo4j console &
bloodhound &
# Drag and drop the ZIP file into BloodHound
```

---

### Key queries to run

Find attack paths — These reveal the fastest routes to DA

```
- Find all Domain Admins
- Shortest Paths to Domain Admins from Owned Principals
- Find Kerberoastable Users with Most Privileges
- Find AS-REP Roastable Users
- Shortest Paths to High Value Targets
- Find Computers with Unconstrained Delegation
- Find Principals with DCSync Rights
```

---

### Mark owned users

Right-click users you've compromised — BloodHound recalculates paths

```
Right-click user > Mark User as Owned
Then run: "Shortest Paths to Domain Admins from Owned Principals"
```

---
