# Summary

[Introduction](README.md)

---

# 📡 Recon & Scanning

- [Recon & Scanning](recon/index.md)

# 🔌 Port Enumeration

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

# 🌐 Web Enumeration

- [Web Enumeration](web-enum/index.md)

# 💉 Web Attacks

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

# 📋 Quick Reference

- [Quick Reference](quick-ref/index.md)

# 🐧 Linux Privilege Escalation

- [Linux Privilege Escalation](linux-pe/index.md)
  - [AUTOMATED ENUMERATION](linux-pe/automated-enumeration.md)
  - [SYSTEM INFORMATION](linux-pe/system-information.md)
  - [SUDO](linux-pe/sudo.md)
  - [SUID / SGID BINARIES](linux-pe/suid-sgid-binaries.md)
  - [CRON JOBS](linux-pe/cron-jobs.md)
  - [FILE PERMISSIONS & CAPABILITIES](linux-pe/file-permissions-capabilities.md)
  - [KERNEL EXPLOITS](linux-pe/kernel-exploits.md)
  - [DOCKER / LXD / CONTAINER ESCAPE](linux-pe/docker-lxd-container-escape.md)
  - [NFS & INTERNAL SERVICES](linux-pe/nfs-internal-services.md)

# 🪟 Windows Privilege Escalation

- [Windows Privilege Escalation](windows-pe/index.md)
  - [AUTOMATED ENUMERATION](windows-pe/automated-enumeration.md)
  - [SYSTEM INFORMATION](windows-pe/system-information.md)
  - [TOKEN PRIVILEGES (POTATO ATTACKS)](windows-pe/token-privileges-potato-attacks.md)
  - [SERVICE MISCONFIGURATIONS](windows-pe/service-misconfigurations.md)
  - [REGISTRY & AUTOLOGON](windows-pe/registry-autologon.md)
  - [CREDENTIAL HUNTING](windows-pe/credential-hunting.md)
  - [SCHEDULED TASKS](windows-pe/scheduled-tasks.md)
  - [KERNEL EXPLOITS](windows-pe/kernel-exploits.md)

# 🏢 Active Directory

- [Active Directory](ad/index.md)
  - [AD ENUMERATION (FROM FOOTHOLD)](ad/ad-enumeration-from-foothold.md)
  - [KERBEROASTING](ad/kerberoasting.md)
  - [AS-REP ROASTING](ad/as-rep-roasting.md)
  - [PASSWORD SPRAYING](ad/password-spraying.md)
  - [LATERAL MOVEMENT](ad/lateral-movement.md)
  - [DOMAIN ESCALATION TO DA](ad/domain-escalation-to-da.md)
  - [POST-EXPLOITATION (AFTER DA)](ad/post-exploitation-after-da.md)

# 🔀 Pivoting & Tunneling

- [Pivoting & Tunneling](pivoting/index.md)
  - [IDENTIFY PIVOT TARGETS](pivoting/identify-pivot-targets.md)
  - [SSH TUNNELING](pivoting/ssh-tunneling.md)
  - [CHISEL (NO SSH NEEDED)](pivoting/chisel-no-ssh-needed.md)
  - [LIGOLO-NG (ADVANCED PIVOTING)](pivoting/ligolo-ng-advanced-pivoting.md)
  - [WINDOWS PORT FORWARDING](pivoting/windows-port-forwarding.md)
  - [PROXYCHAINS CONFIGURATION](pivoting/proxychains-configuration.md)

# ⚡ Speed Hacks

- [Speed Hacks](speed/index.md)
  - [PARALLEL SCANNING - Run these simultaneously in separate terminals](speed/parallel-scanning---run-these-simultaneously-in-se.md)
  - [ONE-LINER CHAINS - Copy-paste enum scripts (replace $IP)](speed/one-liner-chains---copy-paste-enum-scripts-replace.md)
  - [SMART WORDLIST SELECTION - Don't always use the biggest list](speed/smart-wordlist-selection---dont-always-use-the-big.md)
  - [SHORTCUTS & TRICKS - Things people forget](speed/shortcuts-tricks---things-people-forget.md)

# 🌳 Decision Trees

- [Decision Trees](decisions/index.md)

# 🕳️ Rabbit Hole Warnings

- [Rabbit Hole Warnings](rabbit-holes/index.md)

# 🔑 Credential Tracking

- [Credential Tracking](creds/index.md)
