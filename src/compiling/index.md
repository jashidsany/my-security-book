# Compiling Exploits

Cross-compiling C/C++ exploits for Linux and Windows targets.

---

### Compile for Linux (on Kali)

Native compilation — Match target architecture

```bash
gcc exploit.c -o exploit            # 64-bit
gcc -m32 exploit.c -o exploit       # 32-bit
gcc exploit.c -o exploit -static    # Static (no library deps)
gcc exploit.c -o exploit -lpthread -lcrypt  # Common libs
```

---

### Cross-compile for Windows (on Kali)

Use mingw — Create Windows executables from Linux

```bash
x86_64-w64-mingw32-gcc exploit.c -o exploit.exe        # 64-bit
i686-w64-mingw32-gcc exploit.c -o exploit.exe           # 32-bit
x86_64-w64-mingw32-gcc exploit.c -o exploit.exe -lws2_32  # With winsock
# Install: sudo apt install mingw-w64 -y
```

---

### SearchSploit workflow

Find, mirror, and compile

```bash
searchsploit <service version>
searchsploit -m <exploit_id>
head -30 exploit.c   # Read compile instructions in comments
gcc exploit.c -o exploit
python3 -m http.server 80
```

---

### Check target arch first

Match your compilation to the target

```bash
uname -m     # x86_64 = 64-bit, i686 = 32-bit (-m32)
file /bin/ls # Confirms ELF format and arch
```

---

### Common compilation errors

Fix missing dependencies

```bash
# "cannot find -l<library>": apt-get install lib<name>-dev
# "fatal error: <header>.h": apt-get install linux-headers-$(uname -r)
# glibc mismatch: compile with -static
```

---
