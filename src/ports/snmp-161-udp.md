# SNMP (161 UDP)

### Community string brute

```bash
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt $IP
```

> Find valid community strings

**Why:** public/private are defaults

<details>
<summary>Example Output</summary>

```
onesixtyone 10.10.10.5 -c community.txt
10.10.10.5 [public] Linux server 5.4.0
10.10.10.5 [private] Linux server 5.4.0
(FOUND: public and private strings)
```
</details>

---

### Full SNMP walk

```bash
snmpwalk -v2c -c public $IP
```

> Dump all SNMP data

**Why:** Users, processes, software, interfaces

<details>
<summary>Example Output</summary>

```
SNMPv2-MIB::sysDescr.0 = Linux webserver 5.4.0
HOST-RESOURCES::hrSWRunName.1 = apache2
HOST-RESOURCES::hrSWRunName.2 = mysqld
HOST-RESOURCES::hrSWRunParameters.786 = -u admin -p S3cr3tP@ss
```
</details>

---

### Enum users

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.4.1.77.1.2.25
```

> Windows user accounts

**Why:** Username list

<details>
<summary>Example Output</summary>

```
snmpwalk -v2c -c public 10.10.10.5 1.3.6.1.4.1.77.1.2.25
USER: administrator
USER: john
USER: backup_svc
```
</details>

---

### Enum running processes

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.2.1.25.4.2.1.2
```

> Running software

**Why:** Find vulnerable services

<details>
<summary>Example Output</summary>

```
snmpwalk -v2c -c public 10.10.10.5 hrSWRunName
apache2
mysqld
tomcat8
sshd
(Tomcat running = check 8080, look for manager)
```
</details>

---

### Enum installed software

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.2.1.25.6.3.1.2
```

> Installed packages

**Why:** Version info for exploits

<details>
<summary>Example Output</summary>

```
snmpwalk -v2c -c public 10.10.10.5 hrSWInstalledName
apache2 2.4.29
mysql-server 5.7.29
wordpress 5.8
(Version numbers for exploit search)
```
</details>

---

### Enum TCP connections

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.2.1.6.13.1.3
```

> Open TCP ports (internal view)

**Why:** May reveal ports not in nmap

<details>
<summary>Example Output</summary>

```
snmpwalk -v2c -c public 10.10.10.5 tcpConnLocalPort
22, 80, 3306, 8080, 8443
(Port 8080 and 8443 not in nmap = filtered or internal only)
```
</details>

---

### snmp-check tool

```bash
snmp-check $IP -c public
```

> Formatted SNMP enum

**Why:** Cleaner output than snmpwalk

<details>
<summary>Example Output</summary>

```
snmp-check 10.10.10.5 -c public
[*] System information:
  Hostname: webserver
  OS: Linux 5.4.0
[*] User accounts:
  admin, www-data, mysql
[*] Processes:
  apache2, mysqld, cron
```
</details>

---
