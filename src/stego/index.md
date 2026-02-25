# Steganography

Detecting and extracting hidden data in files.

---

### Check file metadata first

exiftool reveals hidden comments, GPS data, embedded files

```bash
exiftool image.jpg
exiftool -b -ThumbnailImage image.jpg > thumbnail.jpg
strings image.jpg | head -50
```

---

### binwalk — Find embedded files

Scan for hidden files inside images, firmware, binaries

```bash
binwalk image.png
binwalk -e image.png         # Extract embedded files
binwalk --dd='.*' image.png  # Extract everything
```

<details>
<summary>Example Output</summary>

```
DECIMAL       HEXADECIMAL     DESCRIPTION
0             0x0             PNG image, 800 x 600
45678         0xB26E          Zip archive data
```
</details>

---

### steghide — Extract from JPEG/BMP

Most common stego tool — Password may be blank or guessable

```bash
steghide info image.jpg
steghide extract -sf image.jpg
steghide extract -sf image.jpg -p ""          # Empty password
steghide extract -sf image.jpg -p "password"  # Known password
```

---

### stegseek — Brute force steghide passwords

Extremely fast steghide cracker — Tries rockyou in seconds

```bash
stegseek image.jpg /usr/share/wordlists/rockyou.txt
stegseek image.jpg wordlist.txt
```

<details>
<summary>Example Output</summary>

```
[i] Found passphrase: "letmein"
[i] Original filename: "secret.txt"
[i] Extracting to "image.jpg.out"
```
</details>

---

### zsteg — PNG/BMP analysis

Detect LSB steganography and other techniques in PNG/BMP

```bash
zsteg image.png
zsteg -a image.png   # Try all methods
zsteg -e "b1,rgb,lsb,xy" image.png  # Extract specific channel
```

---

### Check for hidden text in images

Strings and hex analysis — Sometimes it's just appended text

```bash
strings image.jpg | grep -i "flag\|password\|key\|secret"
xxd image.jpg | tail -20
# Check if file is larger than expected for its dimensions
```

---

### Check file type vs extension

File might be disguised — Extension doesn't match content

```bash
file suspicious_file.jpg
# "suspicious_file.jpg: ASCII text" = not actually a JPEG
# Rename and open as correct type
```

---

### Stego in audio files

Hidden data in WAV/MP3 — Use sonic-visualiser or audacity

```bash
# Spectrogram analysis:
# Open in Audacity → Analyze → Spectrogram
# Hidden messages often visible in spectrogram view

# For WAV files:
steghide extract -sf audio.wav
strings audio.wav | grep -i flag
```

---

### Common stego workflow

Systematic approach — Don't skip steps

```
1. file <file>                    → Verify actual file type
2. exiftool <file>                → Check metadata/comments
3. strings <file> | grep -i flag  → Quick string search
4. binwalk -e <file>              → Extract embedded files
5. steghide extract -sf <file>    → Try empty password
6. stegseek <file> rockyou.txt    → Brute force password
7. zsteg <file>                   → LSB analysis (PNG/BMP)
8. Check file size                → Unusually large = hidden data
```

---
