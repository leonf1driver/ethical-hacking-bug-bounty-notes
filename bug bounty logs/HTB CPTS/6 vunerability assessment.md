

---

## Beveiligingsbeoordelingen (Security Assessments)

Elke organisatie moet regelmatig verschillende soorten beveiligingsbeoordelingen uitvoeren op hun netwerken, computers en applicaties. Het hoofddoel van de meeste assessments is het vinden en bevestigen van kwetsbaarheden, zodat we deze kunnen repareren (patch), beperken (mitigate) of verwijderen. Er zijn verschillende methodologieën om de veiligheid van een computersysteem te testen. Sommige assessments zijn geschikter voor bepaalde netwerken dan andere, maar ze dragen allemaal bij aan het verbeteren van de cybersecurity.

### Kwetsbaarheidsbeoordeling (Vulnerability Assessment)

Kwetsbaarheidsbeoordelingen zijn geschikt voor alle organisaties. Ze zijn gebaseerd op een specifieke beveiligingsstandaard (zoals GDPR of OWASP). Er wordt gewerkt met een checklist om te zien of de organisatie aan de normen voldoet.

- **Doel:** Het identificeren van bekende zwakheden zonder een aanval te simuleren.
    
- **Proces:** Vaak wordt er een scan uitgevoerd, gevolgd door een validatie om aan te tonen dat de kwetsbaarheid echt bestaat (geen 'false positive'). Er wordt echter **geen** poging gedaan tot verdere inbraak (zoals _privilege escalation_ of _lateral movement_).
    

### Penetratietest (Pentest)

Een penetratietest is een gesimuleerde cyberaanval. Testers proberen actief een netwerk binnen te dringen om te zien welke exploits mogelijk zijn. Het cruciale verschil met een echte aanval is de **volledige juridische toestemming**.

Er zijn drie hoofdvormen van pentesten:

- **Black Box:** De tester heeft vooraf geen kennis van het netwerk of de applicaties. Dit simuleert een externe aanvaller.
    
- **Grey Box:** De tester heeft beperkte kennis, vergelijkbaar met het toegangsniveau van een gemiddelde werknemer (bijv. een receptionist).
    
- **White Box:** De tester krijgt volledige toegang tot alle systemen, configuraties en broncode. Het doel is om zoveel mogelijk fouten te vinden in een korte tijd.
    

#### Specialisaties in Pentesting:

- **Applicatie-pentesters:** Beoordelen webapps, mobiele apps en API's.
    
- **Netwerk/Infrastructuur-pentesters:** Beoordelen routers, firewalls, servers en Active Directory.
    
- **Fysieke pentesters:** Testen fysieke beveiliging (kun je een deur openkrijgen, iemand ongemerkt volgen naar het datacenter of via een ventilatieschacht binnenkomen?).
    
- **Social Engineering:** Testen van de menselijke factor (trappen werknemers in phishing of smoesjes?).
    

---

## Kwetsbaarheidsbeoordeling vs. Penetratietest

Hoewel ze elkaar aanvullen, zijn ze verschillend:

- **Vulnerability Assessments** zijn ideaal voor organisaties met een lagere "security maturity" (beveiligingsvolwassenheid). Een pentest zou bij hen simpelweg te veel kwetsbaarheden vinden om te kunnen verwerken.
    
- **Penetratietests** zijn meer geschikt voor organisaties die hun basisbeveiliging al op orde hebben en willen zien hoe een echte aanvalsketen (_attack chain_) eruitziet.
    

---

## Andere Soorten Beveiligingsbeoordelingen

### Security Audits

In tegenstelling tot vrijwillige assessments, zijn audits vaak **verplicht** door externe instanties of overheden. Een bekend voorbeeld is **PCI-DSS** voor bedrijven die creditcardbetalingen accepteren. Het niet naleven hiervan kan leiden tot hoge boetes.

### Bug Bounties

Bedrijven (zoals Microsoft en Apple) nodigen het publiek uit om kwetsbaarheden te vinden in ruil voor een geldbeloning. Dit is vooral effectief voor grote bedrijven met een hoge beveiligingsvolwassenheid.

### Red Team Assessment

Een Red Team voert een ontwijkende _black box_ pentest uit met een specifiek einddoel (bijv. toegang krijgen tot de database met klantgegevens). Ze rapporteren alleen de kwetsbaarheden die nodig waren om dat doel te bereiken, in plaats van een volledige lijst met alle zwakheden.

### Purple Team Assessment

Dit is een samenwerking tussen het **Red Team** (aanval) en het **Blue Team** (verdediging/SOC). In plaats van tegenover elkaar te staan, werken ze samen. Het Blue Team kijkt mee tijdens de aanval om direct te leren hoe ze hun detectie en respons kunnen verbeteren.




---

## Beveiligingsbeoordelingen (Security Assessments)

Elke organisatie moet regelmatig verschillende soorten beveiligingsbeoordelingen uitvoeren op hun netwerken, computers en applicaties. Het hoofddoel van de meeste assessments is het vinden en bevestigen van kwetsbaarheden, zodat we deze kunnen repareren (patch), beperken (mitigate) of verwijderen. Er zijn verschillende methodologieën om de veiligheid van een computersysteem te testen. Sommige assessments zijn geschikter voor bepaalde netwerken dan andere, maar ze dragen allemaal bij aan het verbeteren van de cybersecurity.

### Kwetsbaarheidsbeoordeling (Vulnerability Assessment)

Kwetsbaarheidsbeoordelingen zijn geschikt voor alle organisaties. Ze zijn gebaseerd op een specifieke beveiligingsstandaard (zoals GDPR of OWASP). Er wordt gewerkt met een checklist om te zien of de organisatie aan de normen voldoet.

- **Doel:** Het identificeren van bekende zwakheden zonder een aanval te simuleren.
    
- **Proces:** Vaak wordt er een scan uitgevoerd, gevolgd door een validatie om aan te tonen dat de kwetsbaarheid echt bestaat (geen 'false positive'). Er wordt echter **geen** poging gedaan tot verdere inbraak (zoals _privilege escalation_ of _lateral movement_).
    

### Penetratietest (Pentest)

Een penetratietest is een gesimuleerde cyberaanval. Testers proberen actief een netwerk binnen te dringen om te zien welke exploits mogelijk zijn. Het cruciale verschil met een echte aanval is de **volledige juridische toestemming**.

Er zijn drie hoofdvormen van pentesten:

- **Black Box:** De tester heeft vooraf geen kennis van het netwerk of de applicaties. Dit simuleert een externe aanvaller.
    
- **Grey Box:** De tester heeft beperkte kennis, vergelijkbaar met het toegangsniveau van een gemiddelde werknemer (bijv. een receptionist).
    
- **White Box:** De tester krijgt volledige toegang tot alle systemen, configuraties en broncode. Het doel is om zoveel mogelijk fouten te vinden in een korte tijd.
    

#### Specialisaties in Pentesting:

- **Applicatie-pentesters:** Beoordelen webapps, mobiele apps en API's.
    
- **Netwerk/Infrastructuur-pentesters:** Beoordelen routers, firewalls, servers en Active Directory.
    
- **Fysieke pentesters:** Testen fysieke beveiliging (kun je een deur openkrijgen, iemand ongemerkt volgen naar het datacenter of via een ventilatieschacht binnenkomen?).
    
- **Social Engineering:** Testen van de menselijke factor (trappen werknemers in phishing of smoesjes?).
    

---

## Kwetsbaarheidsbeoordeling vs. Penetratietest

Hoewel ze elkaar aanvullen, zijn ze verschillend:

- **Vulnerability Assessments** zijn ideaal voor organisaties met een lagere "security maturity" (beveiligingsvolwassenheid). Een pentest zou bij hen simpelweg te veel kwetsbaarheden vinden om te kunnen verwerken.
    
- **Penetratietests** zijn meer geschikt voor organisaties die hun basisbeveiliging al op orde hebben en willen zien hoe een echte aanvalsketen (_attack chain_) eruitziet.
    

---

## Andere Soorten Beveiligingsbeoordelingen

### Security Audits

In tegenstelling tot vrijwillige assessments, zijn audits vaak **verplicht** door externe instanties of overheden. Een bekend voorbeeld is **PCI-DSS** voor bedrijven die creditcardbetalingen accepteren. Het niet naleven hiervan kan leiden tot hoge boetes.

### Bug Bounties

Bedrijven (zoals Microsoft en Apple) nodigen het publiek uit om kwetsbaarheden te vinden in ruil voor een geldbeloning. Dit is vooral effectief voor grote bedrijven met een hoge beveiligingsvolwassenheid.

### Red Team Assessment

Een Red Team voert een ontwijkende _black box_ pentest uit met een specifiek einddoel (bijv. toegang krijgen tot de database met klantgegevens). Ze rapporteren alleen de kwetsbaarheden die nodig waren om dat doel te bereiken, in plaats van een volledige lijst met alle zwakheden.

### Purple Team Assessment

Dit is een samenwerking tussen het **Red Team** (aanval) en het **Blue Team** (verdediging/SOC). In plaats van tegenover elkaar te staan, werken ze samen. Het Blue Team kijkt mee tijdens de aanval om direct te leren hoe ze hun detectie en respons kunnen verbeteren.



---

## Kwetsbaarheidsbeoordeling (Vulnerability Assessment)

Een **Vulnerability Assessment** is bedoeld om beveiligingszwakheden binnen een omgeving te identificeren en te categoriseren. Belangrijk hierbij is dat er tijdens zo'n beoordeling **weinig tot geen handmatige exploitatie** (misbruik) plaatsvindt. Het assessment biedt daarnaast stappen voor herstel (_remediation_) om de problemen op te lossen.

Het doel is om risico's te begrijpen zonder ze daadwerkelijk te gebruiken om dieper in het netwerk door te dringen. Afhankelijk van de afspraken kan een klant vragen om bevindingen minimaal te valideren om _false positives_ (onterechte meldingen) uit te sluiten, of simpelweg een volledig rapport van alle scannersoftware-bevindingen op te leveren.

---

## Kernbegrippen

|Begrip|Definitie|
|---|---|
|**Vulnerability** (Kwetsbaarheid)|Een zwakte of bug in applicaties, netwerken of infrastructuur. Deze worden vaak geregistreerd in de **MITRE CVE**-database en krijgen een **CVSS-score** (0-10) op basis van de ernst.|
|**Threat** (Dreiging)|Een proces dat het potentieel van een nadelige gebeurtenis versterkt, zoals een kwaadwillende actor die een kwetsbaarheid probeert te misbruiken.|
|**Exploit**|Code of middelen die specifiek zijn ontworpen om misbruik te maken van een kwetsbaarheid (bijv. te vinden op _Exploit-db_ of _GitHub_).|
|**Risk** (Risico)|De kans dat activa of gegevens schade oplopen. Dit wordt berekend door te kijken naar de **waarschijnlijkheid** en de **impact**.|

> **Kortom:**
> 
> - **Risico:** Iets naars dat _zou kunnen_ gebeuren.
>     
> - **Dreiging:** De actie/actor die het naars _laat_ gebeuren.
>     
> - **Kwetsbaarheid:** De zwakte die de dreiging _mogelijk maakt_.
>     

---

## Asset Management (Beheer van bedrijfsmiddelen)

Een effectieve beveiligingsstrategie begint bij **Asset Inventory**: je kunt niet beschermen wat je niet weet dat je hebt.

### Wat valt onder Data Assets?

1. **On-premises data:** Harde schijven (HDD/SSD) in laptops, servers, USB-sticks en zelfs legacy media zoals tapes.
    
2. **Cloudopslag:** Gegevens bij providers zoals **AWS, Google Cloud (GCP)** en **Microsoft Azure**.
    
3. **SaaS-applicaties:** Gegevens in externe diensten zoals Google Drive, Microsoft Teams, Dropbox en Office 365.
    
4. **Bedrijfsapplicaties:** Zowel lokaal geïnstalleerde software als cloud-gebaseerde apps.
    
5. **Netwerkapparatuur:** Routers, firewalls, switches, en detectiesystemen zoals **IDS/IPS**.
    

Wanneer er apparaten of dataopslag worden toegevoegd of verwijderd, moet dit direct in de inventaris worden bijgewerkt. Zonder een complete lijst is het onmogelijk om een waterdichte beveiliging te garanderen.



---

## Beoordelingsnormen (Assessment Standards)

Zowel penetratietests als kwetsbaarheidsbeoordelingen moeten voldoen aan specifieke normen om geaccrediteerd en geaccepteerd te worden door overheden en juridische instanties. Deze standaarden zorgen ervoor dat het onderzoek grondig en volgens afgesproken methodes wordt uitgevoerd, wat de efficiëntie verhoogt en de kans op een echte aanval verkleint.

### Compliancenormen (Naleving)

Verschillende instanties hanteren hun eigen beveiligingsnormen. Bekende namen zijn:

- **PCI DSS (Payment Card Industry Data Security Standard):** Verplicht voor organisaties die creditcardgegevens verwerken. Vereist strikte segmentatie van de **Cardholder Data Environment (CDE)** en regelmatige scans.
    
- **HIPAA:** Gericht op de bescherming van patiëntgegevens in de zorg. Vereist risicobeoordelingen en identificatie van kwetsbaarheden.
    
- **FISMA:** Richtlijnen voor het beveiligen van Amerikaanse overheidsinformatie en -systemen.
    
- **ISO 27001:** Een wereldwijde standaard voor informatiebeveiliging. Naleving vereist een effectief Information Security Management System (ISMS) en periodieke scans.
    

---

### Penetratietest-standaarden

Een pentest mag nooit zonder regels worden uitgevoerd. Er moet altijd een **getekend juridisch contract** zijn en een duidelijk gedefinieerde **scope** (reikwijdte). Testers moeten schade voorkomen, zo min mogelijk wijzigingen aanbrengen en dataverwijdering beperken (bijvoorbeeld een screenshot maken in plaats van het downloaden van gevoelige bestanden).

De meest gebruikte kaders voor het uitvoeren van een pentest zijn:

#### 1. PTES (Penetration Testing Execution Standard)

Dit is een allesomvattende standaard die de verschillende fasen van een test beschrijft:

1. Interacties voorafgaand aan de opdracht
    
2. Inlichtingen verzamelen (Intelligence Gathering)
    
3. Dreigingsmodellering (Threat Modeling)
    
4. Kwetsbaarheidsanalyse
    
5. Exploitatie
    
6. Post-exploitatie
    
7. Rapportage
    

#### 2. OSSTMM (Open Source Security Testing Methodology Manual)

Een set richtlijnen die zich richt op vijf "kanalen":

- Menselijke beveiliging (Social Engineering)
    
- Fysieke beveiliging
    
- Draadloze communicatie (WiFi, Bluetooth)
    
- Telecommunicatie
    
- Datanetwerken
    

#### 3. NIST (National Institute of Standards and Technology)

Bekend om hun Cybersecurity Framework. Hun pentest-fasen zijn: **Planning**, **Ontdekking**, **Aanval**, en **Rapportage**.

#### 4. OWASP (Open Web Application Security Project)

De absolute autoriteit op het gebied van webapplicatiebeveiliging.

- **WSTG:** Gids voor het testen van webbeveiliging.
    
- **MSTG:** Gids voor mobiele beveiliging.
    
- **Firmware Security:** Methodologie voor het testen van hardware-software

---

## Common Vulnerability Scoring System (CVSS)

Er zijn verschillende manieren om de ernst van kwetsbaarheden te berekenen. Het **Common Vulnerability Scoring System (CVSS)** is de industriestandaard voor deze berekeningen. Hoewel de meeste scanning-tools deze scores automatisch toekennen, is het essentieel dat we begrijpen hoe ze tot stand komen.

Naast CVSS wordt vaak het **Microsoft DREAD**-systeem gebruikt. DREAD helpt professionals risico's te analyseren op een schaal van 1 tot 10 op basis van vijf factoren:

- **D**amage Potential (Schadepotentieel)
    
- **R**eproducibility (Reproduceerbaarheid)
    
- **E**xploitability (Exploiteerbaarheid)
    
- **A**ffected Users (Aantal getroffen gebruikers)
    
- **D**iscoverability (Ontdekbaarheid)
    

---

## CVSS Score-opbouw

De CVSS-score bestaat uit drie hoofdcategorieën die samen de uiteindelijke ernst bepalen.

### 1. Base Metric Group (Basisstatistieken)

Deze groep vertegenwoordigt de intrinsieke kenmerken van een kwetsbaarheid en verandert niet in de loop van de tijd.

- **Exploitability Metrics:** Hoe makkelijk is het om in te breken?
    
    - _Attack Vector:_ Hoe komt de aanvaller binnen (Netwerk, fysiek, etc.)?
        
    - _Attack Complexity:_ Hoe moeilijk is de aanval technisch?
        
    - _Privileges Required:_ Welke rechten heeft de aanvaller nodig?
        
    - _User Interaction:_ Is er hulp van een gebruiker nodig (bijv. klikken op een link)?
        
- **Impact Metrics (De CIA-triade):** Wat zijn de gevolgen?
    
    - _Confidentiality (Vertrouwelijkheid):_ Worden er geheime gegevens gestolen?
        
    - _Integrity (Integriteit):_ Worden gegevens onbedoeld aangepast?
        
    - _Availability (Beschikbaarheid):_ Wordt het systeem onbereikbaar gemaakt?
        

---

### 2. Temporal Metric Group (Tijdsgebonden statistieken)

Deze statistieken veranderen naarmate de tijd verstrijkt, bijvoorbeeld als er een patch uitkomt.

- **Exploit Code Maturity:** Is er al kant-en-klare code beschikbaar om de fout te misbruiken? (Van 'Unproven' tot 'High').
    
- **Remediation Level:** Is er al een oplossing? (Bijv. een 'Workaround', 'Temporary Fix' of een 'Official Fix').
    
- **Report Confidence:** Hoe zeker weten we dat de kwetsbaarheid echt bestaat en de technische details kloppen?
    

---

### 3. Environmental Metric Group (Omgevingsstatistieken)

Deze statistieken zijn specifiek voor jouw organisatie. Als een kwetsbaarheid op papier "kritiek" is, maar het getroffen systeem staat in een afgesloten netwerk zonder internet, kan de _Environmental Score_ lager uitvallen.

---

## CVSS Ernst-niveaus

De uiteindelijke score (0 tot 10) wordt vertaald naar een niveau:

|Score|Ernst (Severity)|
|---|---|
|0.0|None|
|0.1 - 3.9|Low|
|4.0 - 6.9|Medium|
|7.0 - 8.9|High|
|9.0 - 10.0|Critical|

**Voorbeeld:** De beruchte _Windows Print Spooler RCE_ kwetsbaarheid heeft een CVSS Base Score van **8.8 (High)**.

---

**Wil je dat we samen een oefening doen waarbij we handmatig een CVSS-score berekenen voor een fictief scenario, of wil je door naar de classificatie van kwetsbaarheden (CVE's)?*

  



---

## Common Vulnerabilities and Exposures (CVE)

### Open Vulnerability Assessment Language (OVAL)

**OVAL** is een internationale standaard die wordt gebruikt om de huidige staat van een systeem en de aanwezige beveiligingsproblemen te evalueren en gedetailleerd te beschrijven. Het biedt een universele taal om systeemkenmerken te coderen.

**Het OVAL-proces bestaat uit drie stappen:**

1. **Configuraties identificeren:** Vaststellen welke systeeminstellingen getest moeten worden.
    
2. **Systeemstaat evalueren:** De huidige status van het systeem analyseren.
    
3. **Rapportage:** De informatie openbaar maken (bijv. als _Vulnerable_, _Non-compliant_, of _Patched_).
    

Er zijn vier hoofdklassen van OVAL-definities:

- **Vulnerability:** Identificeert kwetsbaarheden.
    
- **Compliance:** Controleert of het systeem voldoet aan het beveiligingsbeleid.
    
- **Inventory:** Controleert of specifieke software aanwezig is.
    
- **Patch:** Controleert of de juiste patches zijn geïnstalleerd.
    

---

### Wat is een CVE?

**Common Vulnerabilities and Exposures (CVE)** is een openbare catalogus van beveiligingsproblemen. Elk probleem krijgt een uniek **CVE-ID** (bijv. CVE-2021-34527). Dit zorgt voor een standaardisatie: onderzoekers, leveranciers en IT-teams weten precies over welk lek ze het hebben.

#### Hoe krijg je een CVE-ID? (Het proces)

Het verkrijgen van een CVE-ID volgt een strikt traject van **Responsible Disclosure** (verantwoorde openbaarmaking):

1. **Identificatie:** Vaststellen of het echt om een nieuwe kwetsbaarheid gaat.
    
2. **Contact met de leverancier:** De onderzoeker neemt contact op met de maker van de software.
    
3. **CNA inschakelen:** Contact opnemen met een _CVE Numbering Authority_ (dit kan de leverancier zelf zijn of een derde partij).
    
4. **Aanvraag indienen:** Het officiële CVE-formulier invullen.
    
5. **Ontvangst & Publicatie:** Na goedkeuring krijgt de onderzoeker een ID. Dit wordt pas openbaar nadat de partijen op de hoogte zijn en er idealiter een oplossing is.
    

---

### Responsible Disclosure vs. Zero-Day

**Responsible Disclosure** is essentieel. Het houdt in dat een onderzoeker een lek eerst privé meldt aan de leverancier, zodat deze een patch kan maken voordat de rest van de wereld (inclusief criminelen) van het lek weet. Als een lek direct openbaar wordt gemaakt zonder dat er een patch is, spreken we van een **Zero-Day (0-day)**. Dit is gevaarlijk omdat aanvallers het lek kunnen misbruiken terwijl verdedigers nog geen kans hebben gehad om zich te beschermen.

---

### Bekende Voorbeelden

- **CVE-2020-5902:** Een kritiek lek in de F5 BIG-IP interface waardoor aanvallers op afstand de volledige controle over het systeem konden overnemen.
    
- **CVE-2021-34527 (PrintNightmare):** Een berucht lek in de Windows Print Spooler-service. Hiermee konden aanvallers servers (waaronder Domain Controllers) volledig overnemen.
    

---

**Nu we de theorie achter de rug hebben, kunnen we overstappen naar de praktijk. Wil je dat ik je help met een introductie in Nessus of OpenVAS, de twee populairste scanners die deze CVE's opsporen?**




---

## Overzicht: Kwetsbaarheidsscans (Vulnerability Scanning)

Zoals eerder besproken, worden kwetsbaarheidsscans uitgevoerd om potentiële zwakheden te identificeren in netwerkapparaten (routers, firewalls, switches), servers, werkstations en applicaties. Dit proces is **geautomatiseerd**. Scanners exploiteren kwetsbaarheden doorgaans niet (op enkele uitzonderingen na), maar hebben een mens nodig om de resultaten handmatig te valideren. Zo wordt bepaald of een melding een echt probleem is dat gerepareerd moet worden, of een **false positive** (foutieve melding) die genegeerd kan worden.

Hoewel een scan vaak deel uitmaakt van een penetratietest, zijn de twee niet hetzelfde. Een scan kan helpen om sneller een breed overzicht te krijgen, maar een echte pentest gaat veel dieper dan alleen een geautomatiseerde tool.

### Statisch vs. Dynamisch Testen

De meeste tools gebruiken een combinatie van twee testmethoden:

- **Statisch testen:** De scanner kijkt naar de versie van een softwarepakket. Als die versie een bekende publieke CVE heeft, wordt dit als kwetsbaarheid gemarkeerd. Dit is niet altijd nauwkeurig, omdat een patch handmatig kan zijn toegepast zonder het versienummer te veranderen.
    
- **Dynamisch testen:** De scanner probeert specifieke (meestal onschadelijke) "payloads" uit op het doelwit, zoals zwakke inloggegevens of SQL-injectie. Als het systeem reageert op deze acties, is de kans groot dat het daadwerkelijk kwetsbaar is.
    

### Scan-methoden: Geauthenticeerd vs. Ongeauthenticeerd

Organisaties moeten beide typen scans continu uitvoeren:

1. **Ongeauthenticeerde scans:** De scanner kijkt van "buitenaf" naar het systeem, zoals een hacker dat zou doen. Dit vindt openstaande poorten en diensten die publiekelijk toegankelijk zijn.
    
2. **Geauthenticeerde scans:** De scanner logt in op het systeem met gebruikersrechten. Hierdoor kan de tool "onder de motorkap" kijken naar missende patches in software, configuratiefouten en lokale kwetsbaarheden die van buitenaf onzichtbaar zijn.
    

---

## Populaire Tools

Er zijn verschillende bekende platforms voor kwetsbaarheidsscanning. De resultaten van deze scans moeten direct worden opgenomen in het **patch management**-programma van de organisatie.

### Nessus (door Tenable)

Nessus is een van de meest bekende commerciële scanners.

- **Nessus Essentials:** De gratis versie voor particulieren en leerdoeleinden. Je kunt hiermee maximaal **16 hosts** scannen. Het is ideaal om de interface en de werking van de scanner te leren kennen.
    

### OpenVAS (door Greenbone)

OpenVAS is een krachtig, **open-source** alternatief.

- Het is volledig gratis en publiekelijk beschikbaar.
    
- Het kan zowel geauthenticeerde als ongeauthenticeerde netwerkscans uitvoeren.
    

### Andere spelers

Andere grote namen in de industrie zijn **Qualys** en **Nexpose** (door Rapid7). Beide bieden proefversies aan voor organisaties 
-

## Aan de slag met Nessus

Laten we kijken hoe we Nessus kunnen downloaden en instellen voor het eerste gebruik. Je kunt deze stappen volgen op je eigen Virtual Machine (VM). Voor de interactieve onderdelen van deze module bieden we een lab-omgeving aan waarin Nessus en OpenVAS al zijn voorgeïnstalleerd.

### 1. Nessus Downloaden

Bezoek de officiële [Downloadpagina](https://www.tenable.com/downloads/nessus) om het juiste installatiebestand voor jouw besturingssysteem te downloaden. In dit voorbeeld gebruiken we het Debian-pakket (`.deb`) voor Ubuntu.

### 2. Gratis Licentie Aanvragen

Ga naar de [Activatiecode-pagina](https://www.tenable.com/products/nessus/nessus-essentials) om een code aan te vragen voor **Nessus Essentials** (de gratis versie). Deze code heb je nodig tijdens de installatie.

### 3. Het pakket installeren

Zodra je het bestand en de code hebt, kun je Nessus installeren via de terminal:

Bash

```
leonwintweer@htb[/htb]$ sudo dpkg -i Nessus-8.15.1-ubuntu910_amd64.deb
```

_(De output zal bevestigen dat de 'nessusd.service' en de bijbehorende symlinks zijn aangemaakt.)_

### 4. De Nessus-service starten

Na de installatie moet je de service handmatig starten:

Bash

```
leonwintweer@htb[/htb]$ sudo systemctl start nessusd.service
```

### 5. Toegang en Configuratie

Je kunt Nessus nu bereiken via je browser op: `https://localhost:8834`.

- **Selecteer versie:** Kies "Nessus Essentials" voor de gratis variant.
    
- **Activatie:** Voer de code in die je per e-mail hebt ontvangen.
    
- **Account:** Maak een gebruikersnaam en een veilig wachtwoord aan.
    
- **Plugins:** Na deze stappen begint Nessus met het compileren van plugins. Dit zijn de "recepten" die de scanner gebruikt om kwetsbaarheden te herkennen. Dit proces kan enige tijd duren.
    

> **Let op:** In de **Nessus Skills Assessment** sectie van deze module is Nessus al geïnstalleerd. Je kunt daar direct inloggen op `https://<IP>:8834` met de gegevens:
> 
> - **Gebruikersnaam:** `htb-student`
>     
> - **Wachtwoord:** `HTB_@cademy_student!`
>     

---

## Navigatie en Instellingen

Zodra de setup is voltooid, kom je op het dashboard terecht. Hier kun je:

- **Scans aanmaken:** Nieuwe scans starten tegen doelwitten.
    
- **Scan Policies:** Regels definiëren voor hoe een scan zich moet gedragen.
    
- **Settings:** Geavanceerde opties configureren, zoals een Proxy-server, SMTP (voor e-mailnotificaties), en instellingen voor prestaties en loggi

---

## Een Nessus-scan uitvoeren

Een nieuwe scan kan worden geconfigureerd door op **New Scan** te klikken en een scantype te selecteren. Scan-templates zijn onderverdeeld in drie categorieën: **Discovery**, **Vulnerabilities**, en **Compliance**.

> **Let op:** Scans kunnen 1 tot 2 uur duren. Het is in de praktijk vaak slim om kwetsbaarheden te analyseren zodra ze binnenkomen, in plaats van te wachten tot de volledige scan klaar is.

### Scantypes (New Scan)

Je hebt de keuze uit verschillende templates:

- **Host Discovery:** Om actieve hosts en open poorten te identificeren.
    
- **Basic Network Scan:** Een algemene scan geschikt voor de meeste systemen.
    
- **Advanced Scan:** Voor volledige controle over alle instellingen.
    
- **Gespecialiseerde scans:** Zoals Malware Scans, Web Application Tests, of scans gericht op specifieke grote kwetsbaarheden.
    

Voor deze oefening kiezen we de **Basic Network Scan**.

### Discovery (Ontdekking)

In de sectie **Host Discovery** kun je de optie inschakelen om te scannen op "fragiele apparaten". Sommige apparaten, zoals oude netwerkprinters, kunnen vastlopen of willekeurige tekst gaan printen als ze gescand worden. Meestal laten we deze instelling uitgeschakeld.

Bij **Port Scanning** bepaal je de diepgang:

- **Common ports:** De meest gebruikte poorten (sneller).
    
- **All ports:** Alle 65.535 poorten (grondiger, maar langzamer).
    
- **Custom:** Een zelfgedefinieerde reeks.
    

### Assessment (Beoordeling)

Onder de categorie **Assessment** kun je specifieke testmethoden configureren:

- **Web Application Scanning:** Nessus kan zoeken naar web-specifieke lekken zoals Remote File Inclusion (RFI). Je kunt hier ook een aangepaste _User Agent_ instellen.
    
- **Brute Force:** Nessus kan proberen in te loggen op ontdekte diensten met behulp van meegeleverde gebruikersnamen en wachtwoordlijsten.
    
- **User Enumeration:** Met technieken zoals **RID Brute Forcing** kan Nessus proberen de namen van lokale of domeingebruikers te achterhalen door specifieke ID-reeksen af te gaan.
    

### Advanced (Geavanceerd)

Op het tabblad **Advanced** staan de **Safe Checks** standaard ingeschakeld. Dit voorkomt dat Nessus tests uitvoert die het doelsysteem of het netwerk zouden kunnen laten crashen.

Andere handige opties hier zijn:

- **Throttling:** De scan vertragen als er netwerkcongestie wordt gedetecteerd.
    
- **Stop on unresponsive hosts:** Stoppen met scannen als een host niet meer reageert.
    
- **Random order:** De lijst met doel-IP's in willekeurige volgorde scannen om detectie door beveiligingssystemen (zoals een IPS) te bemoeilijken.
    

---

**Het configureren van de scan is de eerste stap. Wil je dat we nu kijken naar hoe je de resultaten van zo'n scan interpreteert, of heb je vragen over de specifieke ontdekkingstechnieken zoals RID Brute Forcing?**



## Geavanceerde Instellingen

Nessus biedt tal van geavanceerde opties om scans te verfijnen, zoals scanbeleid (_scan policies_), plugin-beheer en het gebruik van inloggegevens (_credentials_).

### Scanbeleid (Scan Policies)

Scanbeleidsregels zijn aangepaste sjablonen waarmee je specifieke instellingen één keer definieert en opslaat. Dit is handig voor scenario's zoals:

- Een trage, minder opvallende scan (_evasive scan_).
    
- Een scan die specifiek gericht is op webapplicaties.
    
- Een scan voor een specifieke klant met vooraf ingestelde inloggegevens.
    

Je kunt beleidsregels ook exporteren om ze in andere Nessus-scanners te importeren. Je maakt ze aan via de knop **New Policy**. Zodra ze zijn opgeslagen, verschijnen ze onder het tabblad **User Defined** wanneer je een nieuwe scan start.

---

### Nessus Plugins

De "hersenen" van Nessus zijn de plugins, geschreven in de **Nessus Attack Scripting Language (NASL)**. Elke plugin bevat informatie over een specifieke kwetsbaarheid (naam, impact, oplossing) en de methode om deze te testen.

Plugins worden ingedeeld op ernst: **Critical, High, Medium, Low, en Info**. Tenable heeft inmiddels meer dan 145.000 plugins die tienduizenden CVE's dekken.

#### Plugin-regels en False Positives

Soms markeert Nessus iets als een probleem dat in jouw specifieke situatie geen risico vormt. Een voorbeeld is _Microsoft DirectAccess_, dat opzettelijk onveilige encryptie toestaat voor betere prestaties. Om te voorkomen dat dit soort "valse positieven" je rapport vervuilen, kun je **Plugin Rules** aanmaken:

- Ga naar **Resources** > **Plugin Rules**.
    
- Voer het **Plugin ID** en de betreffende host in.
    
- Selecteer de actie **Hide this result** om deze specifieke bevinding te verbergen in toekomstige scans.
    

Dit kan ook worden gebruikt voor zaken die technisch wel kloppen, maar niet direct misbruikt kunnen worden, zoals een _Self-Signed Certificate_.

---

### Scannen met Inloggegevens (Credentialed Scanning)

Dit is een van de krachtigste functies van Nessus. Door inloggegevens te verstrekken, kan Nessus "binnenin" het systeem kijken, wat veel nauwkeuriger resultaten oplevert dan een externe scan.

**Nessus ondersteunt inloggen via:**

- **SSH (Linux):** Met wachtwoord, publieke sleutel, certificaat of Kerberos.
    
- **Windows:** Met wachtwoord, Kerberos, LM-hash of NTLM-hash.
    
- **Databases:** Ondersteuning voor Oracle, PostgreSQL, MySQL, SQL Server, MongoDB, en meer.
    
- **Plaintext:** Voor oudere diensten zoals FTP, HTTP, IMAP en Telnet.
    

> **Lab-notitie:** Om een geauthenticeerde scan in dit lab uit te voeren, gebruik je de volgende gegevens:
> 
> - **Linux:** `htb-student_adm` : `HTB_@cademy_student!`
>     
> - **Windows:** `administrator` : `Academy_VA_adm1!`
>     

Nadat de scan is voltooid, kun je in de output controleren of de authenticatie succesvol was. Als dit is gelukt, zal Nessus een veel diepere lijst met ontbrekende patches en configuratiefouten tonen.

---

**Het gebruik van inloggegevens maakt het verschil tussen een oppervlakkige scan en een professionele audit. Zou je willen dat ik uitleg hoe je de resultaten van een geauthenticeerde scan vergelijkt met een ongeauthenticeerd

  
  

## Werken met Nessus-scanresultaten

Nessus biedt de mogelijkheid om scanresultaten in verschillende rapportformaten te exporteren. Daarnaast kun je ruwe resultaten exporteren om deze te importeren in andere tools (zoals **EyeWitness**, waarmee je automatisch screenshots van ontdekte webapplicaties kunt maken) of om ze te archiveren.

### Nessus-rapporten

Zodra een scan is voltooid, kun je een rapport downloaden in de volgende formaten:

- **.pdf en .html:** Je kunt kiezen voor een _Executive Summary_ of een aangepast rapport. De samenvatting toont een lijst met hosts, het aantal kwetsbaarheden en details zoals ernst, CVSS-score en de naam van de plugin. Elke bevinding bevat een link naar de volledige beschrijving in de Tenable-database.
    
- **.csv:** Hiermee kun je zelf bepalen welke kolommen je exporteert. Dit is ideaal voor analyse in tools zoals **Splunk** of om data te delen met interne teams die verantwoordelijk zijn voor het verhelpen van de lekken (_remediation_).
    

> **Belangrijke opmerking:** Deze scans moeten alleen als bijlage of aanvullende gegevens worden verstrekt. Ze mogen **nooit** als het uiteindelijke eindrapport van een penetratietest of vulnerability assessment aan een klant worden gegeven. Een professioneel rapport vereist menselijke analyse.

---

### Scans exporteren

Nessus biedt ook technische exportformaten:

- **.nessus:** Een XML-bestand met de scaninstellingen en alle plugin-outputs.
    
- **.db (Nessus DB):** Bevat het .nessus-bestand plus de Knowledge Base (KB) van de scan, het audit-traject en eventuele bijlagen.
    

Je kunt het downloaden van rapporten automatiseren via de command-line (CLI) met behulp van de Nessus REST API. Een voorbeeld hiervan is het script `nessus-report-downloader`:

Bash

```
leonwintweer@htb[/htb]$ ./nessus_downloader.rb Nessus 6 Report Downloader 1.0

Voer het IP-adres van de Nessus-server in: 127.0.0.1
Voer de poort in [8834]: 8834
Voer gebruikersnaam in: admin
Voer wachtwoord in: 

Rapportlijst ophalen...
Scan ID  Naam             Laatst gewijzigd     Status         
-------  ----             -------------        ------         
1        Windows_basic    Aug 22, 2020 22:07   completed      

Welk rapport wil je downloaden?: 1
Kies bestandstype: [1] HTML, [2] PDF, [3] CSV...: 3
Bestandslocatie: /assessment_data/inlanefreight/scans/nessus

[+] Rapport gereed voor download...
[+] Rapport gedownload naar: /assessment_data/inlanefreight/scans/nessus/inlanefreight_basic_5y3hxp.csv

Download voltooid!
```

---

## Samenvatting van de resultaten

Het is raadzaam om kwetsbaarheden in het eindrapport te groeperen. Zo krijgt de klant een helder overzicht van welke problemen de meeste impact hebben op welke assets.

**Dit was de laatste pagina over Nessus. Zou je willen dat ik nu overschakel naar de uitleg over OpenVAS, de open-source tegenhanger, of wil je eerst dieper ingaan op hoe je de CSV-data van Nessus kunt filteren voor een presentatie?**