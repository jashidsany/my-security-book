# Dovecot

**What it is:** Open-source IMAP/POP3 mail server for Linux. Usually found alongside Postfix or Exim. Config files often contain plaintext credentials. Rarely directly exploitable but a great source of credentials.

**Default ports:** 110 (POP3), 143 (IMAP), 993 (IMAPS), 995 (POP3S)

**Vuln research:**
- [Dovecot Security Advisories](https://dovecot.org/security)
- [SearchSploit: `searchsploit dovecot`](https://www.exploit-db.com/search?q=dovecot)

---

### Read Dovecot config for creds

Configuration files may contain plaintext passwords

```bash
cat /etc/dovecot/dovecot-users
cat /etc/dovecot/conf.d/10-auth.conf
cat /etc/dovecot/conf.d/auth-passwdfile.conf.ext
# Look for passdb and userdb entries
```

---

### Read mail for credentials

If you can access mail — Users often email passwords

```bash
# Via IMAP:
curl -k imaps://$IP -u user:password
# List mailboxes:
curl -k "imaps://$IP" -u user:password -X "LIST \"\" *"
# Read inbox:
curl -k "imaps://$IP/INBOX" -u user:password -X "FETCH 1:* BODY[TEXT]"
```

---

### Brute force mail credentials

Hydra against IMAP/POP3

```bash
hydra -l user -P wordlist.txt $IP imap
hydra -l user -P wordlist.txt $IP pop3
```

---
