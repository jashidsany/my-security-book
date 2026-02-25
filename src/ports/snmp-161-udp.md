# SNMP (161 UDP)

### Community string brute

Find valid community strings — public/private are defaults

```bash
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings-onesixtyone.txt $IP
```

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

Dump all SNMP data — Users, processes, software, interfaces

```bash
snmpwalk -v2c -c public $IP
```

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

Windows user accounts — Username list

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.4.1.77.1.2.25
```

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

Running software — Find vulnerable services

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.2.1.25.4.2.1.2
```

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

Installed packages — Version info for exploits

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.2.1.25.6.3.1.2
```

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

Open TCP ports (internal view) — May reveal ports not in nmap

```bash
snmpwalk -v2c -c public $IP 1.3.6.1.2.1.6.13.1.3
```

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

Formatted SNMP enum — Cleaner output than snmpwalk

```bash
snmp-check $IP -c public
```

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
