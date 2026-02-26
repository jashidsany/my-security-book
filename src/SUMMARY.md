# Summary

[Introduction](README.md)

---

# Methodology Flowchart

- [Methodology Flowchart](flowchart/index.md)

# CVE Database

- [CVE Database](cve-database/index.md)

# Recon & Scanning

- [Recon & Scanning](recon/index.md)

# Port Enumeration

- [Port Enumeration](ports/index.md)
  - [FTP (21)](ports/ftp-21.md)
  - [SSH (22)](ports/ssh-22.md)
  - [DNS (53)](ports/dns-53.md)
  - [SMTP (25/465/587)](ports/smtp-25465587.md)
  - [POP3 (110/995) / IMAP (143/993)](ports/pop3-110995-imap-143993.md)
  - [SMB (139/445)](ports/smb-139445.md)
  - [SNMP (161 UDP)](ports/snmp-161-udp.md)
  - [LDAP (389/636)](ports/ldap-389636.md)
  - [NFS (2049)](ports/nfs-2049.md)
  - [RPC (111/135)](ports/rpc-111135.md)
  - [MySQL (3306)](ports/mysql-3306.md)
  - [MSSQL (1433)](ports/mssql-1433.md)
  - [RDP (3389)](ports/rdp-3389.md)
  - [WinRM (5985/5986)](ports/winrm-59855986.md)
  - [Redis (6379)](ports/redis-6379.md)

# Web Enumeration

- [Web Enumeration](web-enum/index.md)

# Web Attacks

- [Web Attacks](web-attacks/index.md)
  - [LOCAL FILE INCLUSION (LFI)](web-attacks/local-file-inclusion-lfi.md)
  - [REMOTE FILE INCLUSION (RFI)](web-attacks/remote-file-inclusion-rfi.md)
  - [SQL INJECTION](web-attacks/sql-injection.md)
  - [COMMAND INJECTION](web-attacks/command-injection.md)
  - [FILE UPLOAD ATTACKS](web-attacks/file-upload-attacks.md)
  - [AUTHENTICATION ATTACKS](web-attacks/authentication-attacks.md)
  - [SERVER-SIDE TEMPLATE INJECTION (SSTI)](web-attacks/server-side-template-injection-ssti.md)
  - [XML EXTERNAL ENTITY (XXE)](web-attacks/xml-external-entity-xxe.md)
  - [DESERIALIZATION](web-attacks/deserialization.md)
  - [REVERSE SHELLS & PAYLOADS](web-attacks/reverse-shells-payloads.md)

# Common Applications

- [Common Applications](apps/index.md)
  - [Web Servers & Frameworks](apps/web-servers/index.md)
    - [Apache / Nginx](apps/web-servers/apache-nginx.md)
    - [IIS](apps/web-servers/iis.md)
    - [Node.js / Express](apps/web-servers/nodejs-express.md)
    - [Flask / Django](apps/web-servers/flask-django.md)
    - [PHP](apps/web-servers/php.md)
  - [CMS Platforms](apps/cms/index.md)
    - [WordPress](apps/cms/wordpress.md)
    - [Drupal](apps/cms/drupal.md)
    - [Joomla](apps/cms/joomla.md)
    - [CMS Made Simple](apps/cms/cms-made-simple.md)
    - [Magento](apps/cms/magento.md)
    - [Moodle](apps/cms/moodle.md)
  - [Application Servers & Admin Panels](apps/admin-panels/index.md)
    - [Apache Tomcat](apps/admin-panels/tomcat.md)
    - [Jenkins](apps/admin-panels/jenkins.md)
    - [GitLab](apps/admin-panels/gitlab.md)
    - [Webmin](apps/admin-panels/webmin.md)
    - [Grafana](apps/admin-panels/grafana.md)
    - [phpMyAdmin](apps/admin-panels/phpmyadmin.md)
    - [PostgreSQL / pgAdmin](apps/admin-panels/postgresql.md)
    - [Elasticsearch / Kibana](apps/admin-panels/elasticsearch.md)
    - [Nagios](apps/admin-panels/nagios.md)
    - [Splunk](apps/admin-panels/splunk.md)
    - [Docker API](apps/admin-panels/docker-api.md)
  - [Service-Specific Exploits](apps/services/index.md)
    - [ProFTPD](apps/services/proftpd.md)
    - [vsftpd 2.3.4](apps/services/vsftpd.md)
    - [Samba](apps/services/samba.md)
    - [Exim](apps/services/exim.md)
    - [Dovecot](apps/services/dovecot.md)
    - [OpenSSH](apps/services/openssh.md)
    - [CUPS (Printing)](apps/services/cups.md)

# Linux Privilege Escalation

- [Linux Privilege Escalation](linux-pe/index.md)
  - [AUTOMATED ENUMERATION](linux-pe/automated-enumeration.md)
  - [SYSTEM INFORMATION](linux-pe/system-information.md)
  - [SUDO](linux-pe/sudo.md)
  - [SUID / SGID BINARIES](linux-pe/suid-sgid-binaries.md)
  - [CRON JOBS](linux-pe/cron-jobs.md)
  - [FILE PERMISSIONS & CAPABILITIES](linux-pe/file-permissions-capabilities.md)
  - [PATH HIJACK & LIBRARY HIJACKING](linux-pe/path-hijack.md)
  - [WILDCARD INJECTION](linux-pe/wildcard-injection.md)
  - [KERNEL EXPLOITS](linux-pe/kernel-exploits.md)
  - [DOCKER / LXD / CONTAINER ESCAPE](linux-pe/docker-lxd-container-escape.md)
  - [NFS & INTERNAL SERVICES](linux-pe/nfs-internal-services.md)

# Windows Privilege Escalation

- [Windows Privilege Escalation](windows-pe/index.md)
  - [AUTOMATED ENUMERATION](windows-pe/automated-enumeration.md)
  - [SYSTEM INFORMATION](windows-pe/system-information.md)
  - [TOKEN PRIVILEGES (POTATO ATTACKS)](windows-pe/token-privileges-potato-attacks.md)
  - [SERVICE MISCONFIGURATIONS](windows-pe/service-misconfigurations.md)
  - [SERVICE CREATION & PERSISTENCE](windows-pe/service-creation.md)
  - [REGISTRY & AUTOLOGON](windows-pe/registry-autologon.md)
  - [CREDENTIAL HUNTING](windows-pe/credential-hunting.md)
  - [SCHEDULED TASKS](windows-pe/scheduled-tasks.md)
  - [KERNEL EXPLOITS](windows-pe/kernel-exploits.md)

# Active Directory

- [Active Directory](ad/index.md)
  - [AD ENUMERATION (FROM FOOTHOLD)](ad/ad-enumeration-from-foothold.md)
  - [BLOODHOUND](ad/bloodhound.md)
  - [KERBEROASTING](ad/kerberoasting.md)
  - [AS-REP ROASTING](ad/as-rep-roasting.md)
  - [PASSWORD SPRAYING](ad/password-spraying.md)
  - [NTLM RELAY & RESPONDER](ad/ntlm-relay.md)
  - [LATERAL MOVEMENT](ad/lateral-movement.md)
  - [DELEGATION ATTACKS](ad/delegation.md)
  - [PRINTNIGHTMARE (CVE-2021-1675)](ad/printnightmare.md)
  - [DOMAIN ESCALATION TO DA](ad/domain-escalation-to-da.md)
  - [TICKET ATTACKS (GOLDEN / SILVER)](ad/ticket-attacks.md)
  - [POST-EXPLOITATION (AFTER DA)](ad/post-exploitation-after-da.md)

# Pivoting & Tunneling

- [Pivoting & Tunneling](pivoting/index.md)
  - [IDENTIFY PIVOT TARGETS](pivoting/identify-pivot-targets.md)
  - [SSH TUNNELING](pivoting/ssh-tunneling.md)
  - [CHISEL (NO SSH NEEDED)](pivoting/chisel-no-ssh-needed.md)
  - [LIGOLO-NG (ADVANCED PIVOTING)](pivoting/ligolo-ng-advanced-pivoting.md)
  - [WINDOWS PORT FORWARDING](pivoting/windows-port-forwarding.md)
  - [PROXYCHAINS CONFIGURATION](pivoting/proxychains-configuration.md)

# Buffer Overflow

- [Buffer Overflow](bof/index.md)

# Password Attacks

- [Password Attacks](passwords/index.md)

# Payload Generation

- [Payload Generation](payloads/index.md)

# Client-Side Attacks

- [Client-Side Attacks](client-side/index.md)

# File Transfers

- [File Transfers](transfers/index.md)

# Compiling Exploits

- [Compiling Exploits](compiling/index.md)

# Restricted Shell Escapes

- [Restricted Shell Escapes](shell-escapes/index.md)

# Port Knocking

- [Port Knocking](port-knocking/index.md)

# Steganography

- [Steganography](stego/index.md)

# Persistence

- [Persistence](persistence/index.md)

# Quick Reference

- [Quick Reference](quick-ref/index.md)

# Speed Hacks

- [Speed Hacks](speed/index.md)
  - [PARALLEL SCANNING](speed/parallel-scanning---run-these-simultaneously-in-se.md)
  - [ONE-LINER CHAINS](speed/one-liner-chains---copy-paste-enum-scripts-replace.md)
  - [SMART WORDLIST SELECTION](speed/smart-wordlist-selection---dont-always-use-the-big.md)
  - [SHORTCUTS & TRICKS](speed/shortcuts-tricks---things-people-forget.md)

# Decision Trees

- [Decision Trees](decisions/index.md)

# Rabbit Hole Warnings

- [Rabbit Hole Warnings](rabbit-holes/index.md)

# Credential Tracking

- [Credential Tracking](creds/index.md)
