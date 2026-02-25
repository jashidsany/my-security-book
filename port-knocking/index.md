# Port Knocking

Detecting and executing port knock sequences to open hidden services.

---

### Detect port knocking

Look for knockd configuration — Reveals the knock sequence

```bash
cat /etc/knockd.conf
```

<details>
<summary>Example Output</summary>

```
[openSSH]
    sequence = 7000,8000,9000
    seq_timeout = 5
    command = /sbin/iptables -I INPUT -s %IP% -p tcp --dport 22 -j ACCEPT
```
</details>

---

### Knock with nmap

Send SYN packets to ports in sequence

```bash
for port in 7000 8000 9000; do nmap -Pn --host-timeout 100 --max-retries 0 -p $port $IP; done
```

---

### Knock with knock client

Dedicated knock client — Clean and simple

```bash
# apt install knockd
knock $IP 7000 8000 9000
ssh user@$IP
```

---

### Knock with nc or bash

No tools needed

```bash
# nc:
for port in 7000 8000 9000; do nc -zw1 $IP $port; done
# bash:
for port in 7000 8000 9000; do echo >/dev/tcp/$IP/$port 2>/dev/null; done
```

---

### Find knock sequences

If you can't read knockd.conf

```bash
iptables -L -n
ps aux | grep knock
cat /home/*/.bash_history | grep -i knock
```

---
