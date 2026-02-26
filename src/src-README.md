# Offensive Security Resources

> A comprehensive offensive security reference built for penetration testers and red teamers. 113 pages covering reconnaissance through domain compromise, with copy-paste commands and real-world methodology.

---

## About This Book

This is a hands-on reference designed to be open during engagements, lab work, and exams. Every page follows the same principle: give you the exact command to run, explain why you're running it, and show what the output looks like so you know you're on the right track.

**Who it's for:**

- Pentesters preparing for OSCP, OSEP, or similar certifications
- Red teamers who need a quick-reference during engagements
- Students working through HackTheBox, Proving Grounds, or TryHackMe
- Anyone who wants a structured methodology instead of scattered notes

**What it covers:**

- Full attack chain from initial nmap scan to domain admin
- 19 common applications with default creds and known exploit paths
- 15 port-specific enumeration guides
- Linux and Windows privilege escalation (11+ techniques each)
- Active Directory attacks including BloodHound, delegation, and ticket forging
- Supporting topics: file transfers, shell escapes, payload generation, password cracking, steganography, and persistence

---

## How to Navigate

**Start with the Methodology Flowchart.** It's the first section in the sidebar and acts as a master decision tree for the entire book. When you're stuck, go back to the flowchart.

**Use search.** Press `S` or click the magnifying glass to search across all 113 pages. Search for a port number, tool name, CVE, or technique and you'll find the relevant page instantly.

**Follow the sidebar order.** The book is structured as a sequential methodology. The sidebar flows from reconnaissance at the top through post-exploitation at the bottom. During an engagement, you'll generally work top to bottom.

**The Quick Reference table below** links directly to the most common needs:

| Need | Go To |
|------|-------|
| I don't know where to start | [Methodology Flowchart](flowchart/index.md) |
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
| Hidden data in files | [Steganography](stego/index.md) |
| Maintain access | [Persistence](persistence/index.md) |
| Time management | [Decision Trees](decisions/index.md) |

**Keyboard shortcuts:**

- `S` — Open search
- `←` `→` — Previous / next page
- `T` — Toggle sidebar

---

## Page Format

Every technique in this book follows the same structure so you can scan pages quickly:

```
### Technique Name

Description — why you'd use this and when it applies

​```bash
the exact command to copy and paste
​```

<details>
<summary>Example Output</summary>

What you should expect to see when it works,
so you know you're on the right track.

</details>
```

- **Title** tells you what the technique does
- **Description** tells you why and when to use it
- **Command block** is copy-paste ready (replace `$IP` with the target, `$LHOST` with your attack box)
- **Example Output** (expandable) shows what success looks like

**Variables used throughout the book:**

| Variable | Meaning |
|----------|---------|
| `$IP` | Target IP address |
| `$LHOST` | Your attacker IP address |
| `$DOMAIN` | Target domain name |
| `$DC_IP` | Domain Controller IP |
| `$SUBNET` | Target subnet (e.g., 10.10.10.0) |

---

## Tools You'll Need

This book assumes a Kali Linux attack box. Most tools below come pre-installed. Install anything missing before your engagement or exam.

**Core tools (pre-installed on Kali):**

| Tool | Used For |
|------|----------|
| nmap | Port scanning and service detection |
| gobuster | Directory and vhost brute forcing |
| nikto | Web vulnerability scanner |
| hydra | Online brute force (SSH, FTP, HTTP, etc.) |
| john | Hash cracking |
| hashcat | GPU hash cracking |
| searchsploit | Exploit database search |
| msfvenom | Payload generation |
| netcat (nc) | Reverse shells, file transfers |
| curl / wget | HTTP requests and file downloads |
| enum4linux | SMB/RPC enumeration |
| smbclient / smbmap | SMB share access |
| impacket (full suite) | psexec, secretsdump, ntlmrelayx, mssqlclient, etc. |
| evil-winrm | WinRM shell access |
| crackmapexec | Network-wide credential spraying |
| rpcclient | RPC enumeration |
| ldapsearch | LDAP queries |
| sqlmap | Automated SQL injection (banned on OSCP exam) |
| tcpdump | Packet capture |
| chisel | TCP tunneling without SSH |
| ligolo-ng | Advanced pivoting |

**Install separately:**

```bash
# BloodHound + Neo4j
sudo apt install bloodhound neo4j -y

# Stego tools
sudo apt install steghide stegseek binwalk zsteg exiftool -y

# wpscan
sudo apt install wpscan -y

# mingw (cross-compile for Windows)
sudo apt install mingw-w64 -y

# Python dependencies
pip install bloodhound impacket --break-system-packages

# Ligolo-ng (download from GitHub releases)
# https://github.com/nicocha30/ligolo-ng/releases
```

**Upload to target (keep these ready):**

| Tool | Purpose | Get It |
|------|---------|--------|
| linpeas.sh | Linux privilege escalation scanner | github.com/carlospolop/PEASS-ng |
| winpeas.exe | Windows privilege escalation scanner | github.com/carlospolop/PEASS-ng |
| SharpHound.exe | BloodHound data collector | github.com/BloodHoundAD/SharpHound |
| PowerUp.ps1 | Windows privesc checker | github.com/PowerShellMafia/PowerSploit |
| Rubeus.exe | Kerberos abuse toolkit | github.com/GhostPack/Rubeus |
| Seatbelt.exe | Windows host survey | github.com/GhostPack/Seatbelt |
| chisel | TCP tunnel client | github.com/jpillora/chisel |
| pspy | Monitor Linux processes without root | github.com/DominicBreuker/pspy |
| nc.exe | Netcat for Windows | Pre-compiled in Kali: /usr/share/windows-resources/binaries/ |

**Pre-exam setup checklist:**

```
□ Kali updated: sudo apt update && sudo apt upgrade
□ All tools above installed and tested
□ linpeas/winpeas/SharpHound downloaded to ~/tools/
□ Reverse shell one-liners saved in a cheat sheet
□ VPN connection tested
□ Screenshot tool ready (Flameshot recommended)
□ Note-taking app open (CherryTree, Obsidian, or this book)
□ Terminal multiplexer running (tmux)
```

---

*Built by [Jashid Sany](https://www.jashidsany.com) for penetration testing and red team engagements.*
