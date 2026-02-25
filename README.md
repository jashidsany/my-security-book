# Offensive Security Resources

> A comprehensive offensive security reference covering reconnaissance through domain compromise.

## How to Use This Book

This book is organized as a sequential methodology for penetration testing engagements:

1. **Recon & Scanning** — Find open ports and services
2. **Port Enumeration** — Dig into each service
3. **Web Enumeration** — Map the web attack surface
4. **Web Attacks** — Exploit web vulnerabilities for footholds
5. **Common Applications** — Tomcat, WordPress, Jenkins, Grafana, and more
6. **Linux / Windows PrivEsc** — Escalate to root/SYSTEM
7. **Active Directory** — BloodHound, Kerberoasting, delegation, ticket attacks
8. **Pivoting** — Reach internal networks

Plus exploitation toolbox sections:

- **Buffer Overflow** — Step-by-step stack-based BOF methodology
- **Password Attacks** — Hashcat, John, spraying, wordlist generation
- **Payload Generation** — msfvenom, web shells, reverse shell one-liners
- **Client-Side Attacks** — Macros, HTA files, phishing payloads
- **File Transfers** — Every method for moving files to and from targets
- **Compiling Exploits** — Cross-compiling with gcc and mingw
- **Restricted Shell Escapes** — Breaking out of rbash and limited shells
- **Port Knocking** — Detecting and executing knock sequences

And operational sections:

- **Quick Reference** — Cheat sheets and one-liners
- **Speed Hacks** — Parallel scanning and automation
- **Decision Trees** — When to move on vs. dig deeper
- **Rabbit Holes** — Traps to avoid
- **Credential Tracking** — Log and spray every credential

## Quick Reference

| Need | Go To |
|------|-------|
| Initial port scan commands | [Recon & Scanning](recon/index.md) |
| Enumerate a specific port | [Port Enumeration](ports/index.md) |
| Web app testing | [Web Attacks](web-attacks/index.md) |
| Exploit a known application | [Common Applications](apps/index.md) |
| Generate a payload or shell | [Payload Generation](payloads/index.md) |
| Transfer files to target | [File Transfers](transfers/index.md) |
| Crack a hash | [Password Attacks](passwords/index.md) |
| Linux root | [Linux PrivEsc](linux-pe/index.md) |
| Windows SYSTEM | [Windows PrivEsc](windows-pe/index.md) |
| Domain compromise | [Active Directory](ad/index.md) |
| Reach internal net | [Pivoting](pivoting/index.md) |
| Escape a restricted shell | [Restricted Shell Escapes](shell-escapes/index.md) |
| Compile an exploit | [Compiling Exploits](compiling/index.md) |
| Time management | [Decision Trees](decisions/index.md) |

---

*Built by [Jashid Sany](https://www.jashidsany.com) for penetration testing and red team engagements.*
