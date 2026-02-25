# DNS (53)

### Zone transfer

```bash
dig axfr @$IP <domain>
host -l <domain> $IP
```

> If you know the domain

**Why:** Reveals all subdomains/hosts

<details>
<summary>Example Output</summary>

```
dig axfr @10.10.10.5 domain.htb
domain.htb.     IN  SOA  ns1.domain.htb.
admin.domain.htb. IN A  10.10.10.10
dev.domain.htb.   IN A  10.10.10.11
staging.domain.htb IN A 10.10.10.12
```
</details>

---

### Reverse lookup

```bash
dig -x $IP @$IP
```

> Find domain name

**Why:** Need domain for web enum

<details>
<summary>Example Output</summary>

```
dig -x 10.10.10.5 @10.10.10.5
;; ANSWER SECTION:
5.10.10.10.in-addr.arpa. IN PTR target.corp.local.
(Found hostname: target.corp.local)
```
</details>

---

### Subdomain brute force

```bash
gobuster dns -d <domain> -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -r $IP:53
```

> Find hidden subdomains

**Why:** Virtual hosts, dev sites

<details>
<summary>Example Output</summary>

```
gobuster dns -d corp.local -w subdomains.txt -r 10.10.10.5:53
Found: dev.corp.local
Found: mail.corp.local
Found: vpn.corp.local
```
</details>

---

### Any records

```bash
dig any @$IP <domain>
```

> All DNS records

**Why:** TXT records may have info

<details>
<summary>Example Output</summary>

```
dig any @10.10.10.5 corp.local
corp.local. IN A    10.10.10.5
corp.local. IN MX   mail.corp.local
corp.local. IN TXT  "v=spf1 include:_spf.google.com"
```
</details>

---

### Add to /etc/hosts

```bash
echo '$IP <domain>' >> /etc/hosts
```

> Required for web virtual hosts

**Why:** Web apps may need hostname

<details>
<summary>Example Output</summary>

```
echo '10.10.10.5 target.htb dev.target.htb' >> /etc/hosts

Now: http://target.htb shows different content
than http://10.10.10.5
(Virtual hosting = different sites on same IP)
```
</details>

---
