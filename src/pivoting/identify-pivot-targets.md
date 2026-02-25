# IDENTIFY PIVOT TARGETS

### Check interfaces on foothold

```bash
ip a
ifconfig
ipconfig /all
```

> Multiple NICs = dual-homed

**Why:** Gateway to internal network

<details>
<summary>Example Output</summary>

```
ip a
2: eth0: <BROADCAST> mtu 1500
    inet 10.10.14.5/24
3: eth1: <BROADCAST> mtu 1500
    inet 172.16.1.5/24
(DUAL-HOMED! 172.16.1.0/24 is internal network)
```
</details>

---

### Check routing table

```bash
ip route
route print
netstat -rn
```

> Known networks

**Why:** Map internal subnets

<details>
<summary>Example Output</summary>

```
ip route
default via 10.10.14.1 dev eth0
10.10.14.0/24 dev eth0
172.16.1.0/24 dev eth1
(Can reach 172.16.1.0/24 internal network)
```
</details>

---

### Check ARP cache

```bash
arp -a
ip neigh
```

> Recently contacted hosts

**Why:** Active internal hosts

<details>
<summary>Example Output</summary>

```
arp -a
? (172.16.1.1) at aa:bb:cc:dd:ee:ff [ether] on eth1
? (172.16.1.10) at aa:bb:cc:dd:ee:01 [ether] on eth1
? (172.16.1.20) at aa:bb:cc:dd:ee:02 [ether] on eth1
(3 hosts alive on internal network)
```
</details>

---

### Ping sweep internal network

```bash
for i in $(seq 1 254); do (ping -c 1 10.10.10.$i | grep 'bytes from' &); done
```

> Find live hosts

**Why:** Discover internal targets

<details>
<summary>Example Output</summary>

```
for i in $(seq 1 254); do ping -c 1 172.16.1.$i; done
172.16.1.1: bytes from (gateway)
172.16.1.10: bytes from (host alive)
172.16.1.20: bytes from (host alive)
172.16.1.100: bytes from (host alive)
```
</details>

---

### Port scan from pivot

```bash
for port in 21 22 25 53 80 88 135 139 389 443 445 636 1433 3306 3389 5985 8080; do (echo > /dev/tcp/10.10.10.X/$port) 2>/dev/null && echo "$port open"; done
```

> Bash port scanner (no tools)

**Why:** When you can't upload nmap

<details>
<summary>Example Output</summary>

```
for port in 22 80 445 3389; do
  (echo > /dev/tcp/172.16.1.10/$port) 2>/dev/null && echo "$port open"
done
22 open
80 open
445 open
(Found 3 open ports on internal host)
```
</details>

---
