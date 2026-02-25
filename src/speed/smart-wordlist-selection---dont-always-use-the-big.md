# SMART WORDLIST SELECTION - Don't always use the biggest list

### Quick first pass (30 sec) `~30 sec`

**Wordlists** — 4600 entries. Catches /admin, /uploads, /backup, /config. Run this FIRST always

```bash
gobuster dir -u http://$IP -w /usr/share/seclists/Discovery/Web-Content/common.txt -x php,txt -t 50
```

<details>
<summary>Example Output</summary>

```
/admin (200)
/uploads (301)
/config.php.bak (200)
(Found config backup in 30 seconds)
```
</details>

---

### Medium pass if quick fails `~5 min`

**Wordlists** — 30000 entries. Only run if common.txt found nothing

```bash
gobuster dir -u http://$IP -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,txt -t 50
```

<details>
<summary>Example Output</summary>

```
Run only after common.txt
finds nothing interesting
```
</details>

---

### Custom wordlist from site `N/A`

**Wordlists** — Generate wordlist FROM the site. Great for password brute force and hidden dirs

```bash
cewl http://$IP -w loot/custom_wordlist.txt -d 2 -m 5
```

<details>
<summary>Example Output</summary>

```
Generated 342 words from site
Includes: company names, employee names
Use for hydra brute force too
```
</details>

---

### Username derivation `~2 min`

**Wordlists** — Check naming convention first: try one format, if it works on SMTP/LDAP, use that format for all

```bash
Found users: John Smith, Jane Doe on website
Create users.txt:
john.smith
j.smith
jsmith
john
smith.j
jane.doe
j.doe
jdoe
jane
```

<details>
<summary>Example Output</summary>

```
smtp-user-enum -M VRFY shows:
john.smith VALID
jsmith INVALID
(Use firstname.lastname format)
```
</details>

---
