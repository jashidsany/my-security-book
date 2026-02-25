# OSCP Methodology

> A comprehensive offensive security reference covering reconnaissance through domain compromise.

## How to Use This Book

This book is organized as a sequential methodology for penetration testing engagements:

1. **Recon & Scanning** — Find open ports and services
2. **Port Enumeration** — Dig into each service 
3. **Web Enumeration** — Map the web attack surface
4. **Web Attacks** — Exploit web vulnerabilities for footholds
5. **Linux / Windows PrivEsc** — Escalate to root/SYSTEM
6. **Active Directory** — Enumerate and compromise the domain
7. **Pivoting** — Reach internal networks

Plus operational sections:

- **Speed Hacks** — One-liner chains and parallel scanning
- **Decision Trees** — When to move on vs. dig deeper
- **Rabbit Holes** — Traps to avoid
- **Credential Tracking** — Log and spray every credential

## Quick Reference

| Need | Go To |
|------|-------|
| Initial port scan commands | [Recon & Scanning](recon/index.md) |
| Enumerate a specific port | [Port Enumeration](ports/index.md) |
| Web app testing | [Web Attacks](web-attacks/index.md) |
| Linux root | [Linux PrivEsc](linux-pe/index.md) |
| Windows SYSTEM | [Windows PrivEsc](windows-pe/index.md) |
| Domain compromise | [Active Directory](ad/index.md) |
| Reach internal net | [Pivoting](pivoting/index.md) |
| Exam time management | [Decision Trees](decisions/index.md) |

## Companion Tools

- **[React Checklist App](https://oscp.jashidsany.com)** — Interactive checklist with copy-to-clipboard for use during engagements
- **[stego-drop](https://github.com/jashidsany/stego-drop)** — Steganographic payload embedding tool
- **[web-enum](https://github.com/jashidsany/web-enum)** — Automated web enumeration

---

*Built by [Jashid Sany](https://www.jashidsany.com) for OSCP preparation and red team engagements.*
