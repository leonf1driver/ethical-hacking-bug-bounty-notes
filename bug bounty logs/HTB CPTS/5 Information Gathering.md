# DNS & Subdomain Reconnaissance - Lab Report

## Datum

24 januari 2026

## Overzicht

Deze lab-sessie richtte zich op DNS-reconnaissance en subdomain enumeration voor het domein `inlanefreight.htb`. Het doel was om de DNS-infrastructuur in kaart te brengen en verborgen subdomeinen te ontdekken via verschillende enumeratie-technieken.

---

## Lab 1: DNS Zone Transfer - inlanefreight.htb

### Doelstelling

Een DNS Zone Transfer uitvoeren om de volledige DNS-infrastructuur van `inlanefreight.htb` bloot te leggen.

### Target Informatie

- **Domein**: `inlanefreight.htb`
- **DNS Server**: `10.129.5.255`
- **Lab Duration**: 119 minuten

### Uitgevoerd Commando

bash

````bash
dig axfr inlanefreight.htb @10.129.5.255
```

### Resultaten

#### Zone Transfer Statistieken
- **Totaal aantal DNS records**: 22
- **Query tijd**: 88 ms
- **Transfer grootte**: 594 bytes

#### Ontdekte Subdomeinen en IP-Adressen

##### Administratieve Systemen
| Subdomain | IP-adres | Functie |
|-----------|----------|---------|
| `admin.inlanefreight.htb` | `10.10.34.2` | Admin interface |
| `ftp.admin.inlanefreight.htb` | `10.10.34.2` | FTP service op admin |
| `admin.internal.inlanefreight.htb` | `10.10.1.11` | Interne admin |

##### Domain Controllers
| Subdomain | IP-adres | Type |
|-----------|----------|------|
| `dc1.inlanefreight.htb` | `10.10.34.16` | Primary DC |
| `dc2.inlanefreight.htb` | `10.10.34.11` | Secondary DC |

##### Ontwikkel- en Testomgevingen
| Subdomain | IP-adres | Functie |
|-----------|----------|---------|
| `dev.ir.inlanefreight.htb` | `10.10.45.6` | Development (IR) |
| `test1.inlanefreight.htb` | `10.10.34.101` | Test server |

##### Interne Services
| Subdomain | IP-adres | Service |
|-----------|----------|---------|
| `internal.inlanefreight.htb` | `127.0.0.1` | Localhost |
| `wsus.internal.inlanefreight.htb` | `10.10.1.240` | Windows Update Services |
| `ir.inlanefreight.htb` | `10.10.45.5` | Incident Response |
| `resources.inlanefreight.htb` | `10.10.34.100` | Resource server |
| `securemessaging.inlanefreight.htb` | `10.10.34.52` | Messaging platform |

##### Publieke Services
| Subdomain | IP-adres | Functie |
|-----------|----------|---------|
| `careers.inlanefreight.htb` | `10.10.34.50` | Recruitment portal |
| `www1.inlanefreight.htb` | `10.10.34.111` | Webserver 1 |
| `ww02.inlanefreight.htb` | `10.10.34.112` | Webserver 2 |

##### Regionale Infrastructure (US)
| Subdomain | IP-adres | Functie |
|-----------|----------|---------|
| `us.inlanefreight.htb` | `10.10.200.5` | US hoofdserver |
| `cluster14.us.inlanefreight.htb` | `10.10.200.14` | US Cluster 14 |
| `messagecenter.us.inlanefreight.htb` | `10.10.200.10` | US Message Center |

##### Name Servers
| Subdomain | IP-adres |
|-----------|----------|
| `ns.inlanefreight.htb` | `127.0.0.1` |

### Beantwoorde Vragen

#### Vraag 1: Aantal DNS Records
**Antwoord**: `22`

**Bron**: Laatste regel van de dig output
```
;; XFR size: 22 records (messages 1, bytes 594)
```

#### Vraag 2: IP-adres voor ftp.admin.inlanefreight.htb
**Antwoord**: `10.10.34.2`

**Bron**: Zone transfer record
```
ftp.admin.inlanefreight.htb. 604800 IN A 10.10.34.2
````

#### Vraag 3: Hoogste IP-adres in 10.10.200.x range

**Antwoord**: `10.10.200.14`

**Analyse**:

- `us.inlanefreight.htb`: `10.10.200.5`
- `messagecenter.us.inlanefreight.htb`: `10.10.200.10`
- `cluster14.us.inlanefreight.htb`: `10.10.200.14` ← **Hoogste**

---

## Lab 2: Subdomain Bruteforcing - inlanefreight.com

### Doelstelling

Via brute-force methoden het ontbrekende subdomain vinden dat niet in de lijst van bekende subdomeinen staat.

### Bekende Subdomeinen (gegeven)

- `www.inlanefreight.com`
- `ns1.inlanefreight.com`
- `ns2.inlanefreight.com`
- `ns3.inlanefreight.com`
- `blog.inlanefreight.com`
- `support.inlanefreight.com`
- `customer.inlanefreight.com`

### Uitgevoerd Commando

bash

```bash
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
```

### Parameters Uitleg

- `--enum`: Combineert meerdere enumeratie-technieken
- `-f`: Specificeer wordlist voor brute-force
- `-r`: Recursieve subdomain enumeration

### Resultaat

**Ontbrekend subdomain**: `my.inlanefreight.com`

**IP-adres**: `134.209.24.248`

**Analyse**: Het `my` subdomain wijst typisch op een klanten-/medewerkerportaal en is een hoogwaardig doelwit voor verdere reconnaissance.

---

## WHOIS Reconnaissance

### PayPal.com Analyse

#### Uitgevoerd Commando

bash

```bash
whois paypal.com
```

#### Belangrijkste Bevindingen

- **Registrar**: MarkMonitor Inc.
- **IANA ID**: `292`
- **Creation Date**: 1999-07-15
- **Expiry Date**: 2026-07-15
- **Registrant**: PayPal Inc.

**Antwoord IANA ID vraag**: `292`

### Tesla.com Analyse

#### Uitgevoerd Commando

bash

```bash
whois tesla.com
```

#### Belangrijkste Bevindingen

- **Registrar**: MarkMonitor Inc. (IANA ID: 292)
- **Creation Date**: 1992-11-04
- **Registrant Organization**: DNStination Inc. (proxy service)
- **Admin Email**: `admin@dnstinations.com`

**Antwoord Admin Email vraag**: `admin@dnstinations.com`

**Opmerking**: Tesla gebruikt een proxy-service voor privacy, wat gebruikelijk is voor grote bedrijven.

---

## DNS Record Lookups

### InlaneFreight.com

#### A Record

bash

```bash
dig inlanefreight.com +short
```

**Resultaat**: `134.209.24.248`

### Reverse DNS Lookup

#### PTR Record voor 134.209.24.248

bash

```bash
dig -x 134.209.24.248
```

**Resultaat**: `inlanefreight.com`

**Analyse**: De reverse lookup bevestigt dat het IP-adres officieel bij het domein hoort, wat duidt op een legitieme configuratie.

### Facebook.com Mail Records

#### MX Record

bash

```bash
dig facebook.com MX
```

**Resultaat**: `smtpin.vvv.facebook.com` (prioriteit 10)

**Analyse**: Facebook beheert zijn eigen mailinfrastructuur in plaats van externe diensten te gebruiken.

---

## Beveiligingsanalyse

### High-Value Targets Geïdentificeerd

#### Kritieke Infrastructuur

1. **Domain Controllers** (`dc1`, `dc2`)
    - Kern van Active Directory
    - Hoogste prioriteit voor aanvallers
    - IP's: `10.10.34.16`, `10.10.34.11`
2. **WSUS Server** (`wsus.internal`)
    - Kan gebruikt worden voor privilege escalation
    - Controleert software updates voor het netwerk
    - IP: `10.10.1.240`

#### Administratieve Toegangspunten

3. **Admin Interfaces**
    - `admin.inlanefreight.htb` (10.10.34.2)
    - `admin.internal.inlanefreight.htb` (10.10.1.11)
    - Mogelijk onbeveiligde beheerpanels
4. **FTP Service** (`ftp.admin`)
    - Deelt IP met admin interface
    - Mogelijk kwetsbaar voor anonymous access
    - IP: `10.10.34.2`

#### Ontwikkelomgevingen

5. **Development Servers**
    - `dev.ir.inlanefreight.htb` (10.10.45.6)
    - `test1.inlanefreight.htb` (10.10.34.101)
    - Vaak minder beveiligd dan productie
    - Kunnen gevoelige test-data bevatten

### Kwetsbaarheden

#### Zone Transfer Misconfiguratie

**Ernst**: Kritiek

**Beschrijving**: De DNS-server staat ongeautoriseerde zone transfers toe, wat de volledige interne infrastructuur blootlegt.

**Impact**:

- Volledige inventarisatie van interne systemen
- Identificatie van kritieke targets zonder detectie
- Basis voor targeted attacks

**Remediation**:

apache

```apache
# Beperk zone transfers tot vertrouwde servers
allow-transfer { 10.10.34.16; 10.10.34.11; };
```

---

## Geleerde Technieken

### 1. DNS Zone Transfers (AXFR)

- **Tool**: `dig`
- **Effectiviteit**: Zeer hoog (indien server misconfigured)
- **Detecteerbaarheid**: Laag (slechts één request)
- **Use Case**: Snelle complete inventarisatie

### 2. Subdomain Brute-forcing

- **Tool**: `dnsenum`
- **Effectiviteit**: Gemiddeld tot hoog
- **Detecteerbaarheid**: Hoog (veel requests)
- **Use Case**: Als zone transfer mislukt

### 3. WHOIS Reconnaissance

- **Tool**: `whois`
- **Effectiviteit**: Basis informatie verzameling
- **Detecteerbaarheid**: Geen (publieke database)
- **Use Case**: Eigenaarsinformatie, registrar details

### 4. Reverse DNS

- **Tool**: `dig -x`
- **Effectiviteit**: Validatie van IP-domein relatie
- **Detecteerbaarheid**: Geen
- **Use Case**: Bevestiging van legitimiteit

---

## Aanbevelingen voor Verdere Reconnaissance

### Volgende Stappen

1. **Fingerprinting**
    - Identificeer webserver software en versies
    - Zoek naar bekende CVE's
    - Tools: `whatweb`, `wappalyzer`, `nmap`
2. **Virtual Host Discovery**
    - Brute-force VHosts op ontdekte IP's
    - Zoek naar hidden webapplicaties
    - Tool: `gobuster vhost`
3. **Port Scanning**
    - Scan alle ontdekte IP's voor open poorten
    - Identificeer services en versies
    - Tool: `nmap -sV -sC`
4. **Web Crawling**
    - Crawl ontdekte websites voor endpoints
    - Zoek naar admin panels, API's
    - Tools: `gospider`, `hakrawler`

---

## Tijdlijn

|Tijd|Activiteit|Resultaat|
|---|---|---|
|08:20|DNS Zone Transfer inlanefreight.htb|22 records gevonden|
|08:26|Reverse DNS lookup|Domein gevalideerd|
|08:27|MX record Facebook|Mail infrastructure geïdentificeerd|
|09:23|Subdomain bruteforce|`my.inlanefreight.com` ontdekt|

---

## Conclusie

Deze reconnaissance-sessie heeft aangetoond hoe effectief DNS-enumeration is voor het in kaart brengen van een organisatie's infrastructuur. De succesvolle zone transfer onthulde kritieke interne systemen die normaal verborgen zouden blijven.

**Key Takeaways**:

1. Zone transfers kunnen bij misconfiguratie de volledige infrastructuur blootleggen
2. Combinatie van passieve (WHOIS) en actieve (zone transfer, brute-force) technieken is het meest effectief
3. Zelfs publieke DNS-data kan waardevolle inzichten geven over de technologische stack van een organisatie

**Cubes Verdiend**: 45 (9 vragen × 5 cubes each)

---

## Referenties

### Tools Gebruikt

- `dig` (9.20.15-2-Debian)
- `dnsenum` (1.3.1)
- `whois` (command-line utility)

### Wordlists

- `/usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`
- `/usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt`

### Documentatie

- RFC 1035 (Domain Names - Implementation and Specification)
- RFC 5936 (DNS Zone Transfer Protocol - AXFR)
- HTB Academy - Information Gathering Web Edition


# HTB Academy - Information Gathering (Web Edition)

## Volledige Module Rapportage

**Datum:** 25 januari 2026  
**Module:** Information Gathering - Web Edition  
**Status:** 100% Voltooid  
**Cubes Verdiend:** 100+  
**Tijdsinvestering:** ~12 uur

---

## 📋 Inhoudsopgave

1. [Executive Summary](#executive-summary)
2. [Module Overzicht](#module-overzicht)
3. [WHOIS Reconnaissance](#whois-reconnaissance)
4. [DNS & Subdomains](#dns--subdomains)
5. [Virtual Host Discovery](#virtual-host-discovery)
6. [Fingerprinting](#fingerprinting)
7. [Web Crawling](#web-crawling)
8. [Search Engine Discovery](#search-engine-discovery)
9. [Web Archives](#web-archives)
10. [Skills Assessment](#skills-assessment)
11. [Geleerde Lessen](#geleerde-lessen)
12. [Tool Arsenal](#tool-arsenal)

---

## Executive Summary

Deze module behandelt de fundamentele technieken voor het verzamelen van informatie over webapplicaties en online infrastructuur. Door middel van zowel **passieve** als **actieve** reconnaissance methoden wordt een volledig beeld opgebouwd van het digitale landschap van een doelwit.

### Kernconcepten

**Passieve Reconnaissance:**

- Geen directe interactie met doelsystemen
- Gebruik van publieke bronnen (WHOIS, DNS, archieven)
- Zeer laag detectierisico

**Actieve Reconnaissance:**

- Directe interactie met doelsystemen
- Port scanning, directory fuzzing, crawling
- Hoger detectierisico, maar meer gedetailleerde informatie

---

## Module Overzicht

### Doelstellingen

1. **Asset Identificatie**: Het blootleggen van alle publiekelijk toegankelijke componenten
2. **Verborgen Informatie**: Ontdekken van onbedoeld geëxposeerde data
3. **Attack Surface Analysis**: In kaart brengen van potentiële kwetsbaarheden
4. **Intelligence Gathering**: Verzamelen van bruikbare informatie voor verdere exploitatie

---

## WHOIS Reconnaissance

### Theorie

WHOIS is een query-protocol voor het raadplegen van databases met informatie over geregistreerde internetbronnen (domeinnamen, IP-adressen).

### Belangrijke WHOIS Velden

|Veld|Beschrijving|Reconnaissance Waarde|
|---|---|---|
|**Registrar**|Bedrijf waar domein is geregistreerd|Identificatie van hostingprovider|
|**Registrant**|Eigenaar van het domein|Mogelijk contactinformatie|
|**Creation Date**|Registratiedatum|Bepalen domeinleeftijd|
|**Name Servers**|DNS-servers voor het domein|Infrastructuur mapping|
|**DNSSEC**|Security status|Beveiligingsniveau inschatting|

### Praktische Oefeningen

#### Vraag 1: PayPal.com IANA ID

bash

````bash
whois paypal.com
```

**Resultaat:**
```
Registrar: MarkMonitor Inc.
Registrar IANA ID: 292
Creation Date: 1999-07-15
````

**Antwoord:** `292`

**Analyse:** PayPal gebruikt MarkMonitor, een premium registrar service specifiek voor grote bedrijven. Dit duidt op een professionele security posture.

---

#### Vraag 2: Tesla.com Admin Email

bash

````bash
whois tesla.com
```

**Resultaat:**
```
Registrant Organization: DNStination Inc.
Admin Email: admin@dnstinations.com
````

**Antwoord:** `admin@dnstinations.com`

**Analyse:** Tesla gebruikt een **proxy service** (DNStination) om hun echte administratieve contactgegevens te verbergen - een standaard privacy-maatregel voor grote organisaties.

---

## DNS & Subdomains

### DNS Fundamentals

Het Domain Name System (DNS) is het "telefoonboek" van het internet. Het vertaalt menselijk leesbare domeinnamen naar IP-adressen die computers gebruiken.

### Belangrijke DNS Record Types

|Record Type|Functie|Voorbeeld|
|---|---|---|
|**A**|IPv4 adres|`example.com. IN A 192.0.2.1`|
|**AAAA**|IPv6 adres|`example.com. IN AAAA 2001:db8::1`|
|**CNAME**|Alias (canonical name)|`blog.example.com. IN CNAME webserver.net.`|
|**MX**|Mail server|`example.com. IN MX 10 mail.example.com.`|
|**NS**|Name server|`example.com. IN NS ns1.example.com.`|
|**TXT**|Tekst informatie|`example.com. IN TXT "v=spf1 mx -all"`|
|**SOA**|Start of Authority|Administratieve zone info|
|**PTR**|Reverse DNS lookup|IP → Hostname|

### DNS Reconnaissance Vragen

#### Vraag 1: InlaneFreight.com IP Address

bash

```bash
dig inlanefreight.com +short
```

**Resultaat:** `134.209.24.248`

---

#### Vraag 2: Reverse DNS Lookup

bash

````bash
dig -x 134.209.24.248
```

**Resultaat:**
```
248.24.209.134.in-addr.arpa. 335 IN PTR inlanefreight.com.
````

**Antwoord:** `inlanefreight.com`

**Analyse:** De reverse lookup bevestigt dat het IP-adres officieel bij het domein hoort - een indicatie van legitieme configuratie.

---

#### Vraag 3: Facebook.com Mail Records

bash

````bash
dig facebook.com MX +short
```

**Resultaat:**
```
10 smtpin.vvv.facebook.com.
````

**Antwoord:** `smtpin.vvv.facebook.com`

**Analyse:** Facebook beheert zijn eigen mailinfrastructuur in plaats van externe diensten te gebruiken (zoals Google Workspace).

---

### DNS Zone Transfer (AXFR)

#### Lab: InlaneFreight.htb Zone Transfer

**Target:** `10.129.5.255`  
**Duration:** 119 minuten

bash

```bash
dig axfr inlanefreight.htb @10.129.5.255
```

**Belangrijkste Bevindingen:**

##### Kritieke Infrastructuur

|Subdomain|IP-adres|Functie|
|---|---|---|
|`dc1.inlanefreight.htb`|`10.10.34.16`|Domain Controller 1|
|`dc2.inlanefreight.htb`|`10.10.34.11`|Domain Controller 2|
|`wsus.internal.inlanefreight.htb`|`10.10.1.240`|Windows Update Services|

##### Administratieve Toegangspunten

|Subdomain|IP-adres|Risico|
|---|---|---|
|`admin.inlanefreight.htb`|`10.10.34.2`|Admin interface|
|`ftp.admin.inlanefreight.htb`|`10.10.34.2`|FTP op admin|
|`admin.internal.inlanefreight.htb`|`10.10.1.11`|Interne admin|

##### Ontwikkelomgevingen

|Subdomain|IP-adres|Notities|
|---|---|---|
|`dev.ir.inlanefreight.htb`|`10.10.45.6`|Development|
|`test1.inlanefreight.htb`|`10.10.34.101`|Test server|

**Totaal:** 22 DNS records ontdekt

**Security Implication:** Dit is een **kritieke misconfiguratie**. De DNS-server staat ongeautoriseerde zone transfers toe, wat de volledige interne infrastructuur blootlegt zonder enige authenticatie.

---

### Subdomain Enumeration

#### Brute-force met DNSenum

**Doelwit:** `inlanefreight.com`  
**Bekende subdomains:** www, ns1, ns2, ns3, blog, support, customer

bash

```bash
dnsenum --enum inlanefreight.com \
  -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt -r
```

**Ontdekt:** `my.inlanefreight.com`

**Analyse:** Het "my" subdomein wijst typisch op een klanten-/medewerkerportaal - een hoogwaardig doelwit voor verdere reconnaissance.

---

## Virtual Host Discovery

### Conceptueel Verschil: Subdomain vs VHost

**Subdomain:**

- Heeft eigen DNS record
- Kan wijzen naar verschillend IP-adres
- Voorbeeld: `mail.google.com` → `172.217.x.x`

**Virtual Host (VHost):**

- Geen aparte DNS record nodig
- Meerdere sites op **hetzelfde IP**
- Server beslist op basis van `Host:` header
- Voorbeeld: `admin.site.com` en `dev.site.com` → beide `192.0.2.1`

### VHost Discovery Lab

**Target:** `83.136.253.5:59336`  
**Domein:** `inlanefreight.htb`

#### Stap 1: /etc/hosts Configuratie

bash

```bash
echo "83.136.253.5 inlanefreight.htb" | sudo tee -a /etc/hosts
```

#### Stap 2: VHost Scanning

bash

```bash
gobuster vhost \
  -u http://inlanefreight.htb:59336 \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt \
  --append-domain -t 50
```

**Resultaten:**

|VHost|Status|Size|Functie|
|---|---|---|---|
|`web17611.inlanefreight.htb`|200|106|Webservice|
|`vm5.inlanefreight.htb`|200|96|VM beheer|
|`browse.inlanefreight.htb`|200|102|Browse functie|
|`admin.inlanefreight.htb`|200|100|✅ Admin panel|
|`support.inlanefreight.htb`|200|104|✅ Support portal|

**Antwoorden:**

- Prefix "web": `web17611.inlanefreight.htb`
- Prefix "vm": `vm5.inlanefreight.htb`
- Prefix "br": `browse.inlanefreight.htb`
- Prefix "a": `admin.inlanefreight.htb`
- Prefix "su": `support.inlanefreight.htb`

**Belangrijk Inzicht:** De standaard wordlists (common.txt) waren **onvoldoende**. Pas met een lijst van 20,000 subdomains werden de numerieke varianten (web17611, vm5) ontdekt.

---

## Fingerprinting

### Doelstelling

Identificeren van de exacte software, versies en besturingssystemen die op de webservers draaien.

### Lab Target

- **IP:** `10.129.6.146`
- **VHosts:** `app.inlanefreight.local`, `dev.inlanefreight.local`

#### Stap 1: Banner Grabbing

bash

````bash
curl -I http://app.inlanefreight.local
```

**Output:**
```
Server: Apache/2.4.41 (Ubuntu)
Set-Cookie: 72af8f2b24261272e581a49f5c56de40=...
Expires: Wed, 17 Aug 2005 00:00:00 GMT
```

**Analyse:**
1. **Apache versie:** `2.4.41`
2. **Besturingssysteem:** `Ubuntu`
3. **CMS identificatie:** De specifieke cookie-hash en de `Expires: 2005` datum zijn **karakteristiek voor Joomla**

#### Resultaten

| Vraag | Antwoord | Bron |
|-------|----------|------|
| Apache versie (app) | `2.4.41` | Server header |
| CMS (app) | `Joomla` | Cookie pattern + Expires header |
| OS (dev) | `Ubuntu` | Server header |

---

## Web Crawling

### Theorie

**Crawling** (ook wel spidering) is het geautomatiseerd doorzoeken van een website door alle links te volgen en content te extraheren.

### Strategieën

**Breadth-First Crawling:**
- Verkent eerst alle links op hetzelfde niveau
- Goed voor breed overzicht van de structuur

**Depth-First Crawling:**
- Volgt één pad zo diep mogelijk
- Nuttig voor het vinden van diep verborgen content

### Extracteerbare Data

| Data Type | Waarde voor Recon |
|-----------|-------------------|
| **Links** | Website structuur, verborgen pagina's |
| **Comments** | Developer notes, TODO's, credentials |
| **Metadata** | Auteursnamen, software versies |
| **Emails** | Social engineering targets |
| **Formulieren** | Input validatie, injection punten |
| **Bestanden** | Backups (.bak), configs (.conf) |

### robots.txt - De Wegwijzer

Het `robots.txt` bestand is bedoeld voor zoekmachines maar onthult vaak juist **gevoelige directories**.

**Voorbeeld:**
```
User-agent: *
Disallow: /admin/
Disallow: /backup/
Disallow: /config/
````

⚠️ **Security Implication:** Dit bestand vertelt aanvallers exact wáár de interessante mappen staan.

---

### Skills Assessment Crawling

**Target:** `inlanefreight.com` (publiek domein)

#### ReconSpider Scan

bash

```bash
wget -O ReconSpider.zip https://academy.hackthebox.com/storage/modules/144/ReconSpider.v1.2.zip
unzip ReconSpider.zip
pip3 install scrapy --break-system-packages
python3 ReconSpider.py http://inlanefreight.com
```

**Resultaat in `results.json`:**

json

````json
{
  "comments": [
    "<!-- TO-DO: change the location of future reports to inlanefreight-comp133.s3.amazonaws.htb -->"
  ]
}
```

**Antwoord:** `inlanefreight-comp133.s3.amazonaws.htb`

**Analyse:** Een developer heeft per ongeluk een toekomstige AWS S3 bucket locatie als TODO achtergelaten in de broncode.

---

## Search Engine Discovery

### Google Dorking

Google Dorks zijn geavanceerde zoekoperators die verborgen informatie kunnen blootleggen.

### Belangrijkste Operators

| Operator | Functie | Voorbeeld |
|----------|---------|-----------|
| `site:` | Beperkt tot specifiek domein | `site:example.com` |
| `inurl:` | Zoekt term in URL | `inurl:admin` |
| `filetype:` | Specifieke bestandstypes | `filetype:pdf` |
| `intitle:` | Term in paginatitel | `intitle:"index of"` |
| `intext:` | Term in body tekst | `intext:password` |
| `cache:` | Cached versie | `cache:example.com` |

### Praktische Dorks

**Login pagina's vinden:**
```
site:example.com inurl:login
site:example.com (inurl:admin OR inurl:login)
```

**Gevoelige bestanden:**
```
site:example.com filetype:pdf "confidential"
site:example.com (filetype:xls OR filetype:docx)
```

**Configuratiebestanden:**
```
site:example.com inurl:config.php
site:example.com (ext:conf OR ext:cnf)
````

---

## Web Archives

### Wayback Machine

De Wayback Machine (web.archive.org) archiveert sinds 1996 snapshots van websites. Dit is een goudmijn voor reconnaissance.

### Waarom Web Archives belangrijk zijn

1. **Verwijderde Content**: Oude bestanden/mappen die nu offline zijn
2. **Leaked Credentials**: Testaccounts die vergeten zijn te verwijderen
3. **Technology Evolution**: Welke software werd vroeger gebruikt
4. **Passive Recon**: Geen enkel spoor bij het doelwit

### Praktische Vragen

#### Vraag 1: HackTheBox Labs (8 aug 2018)

**Antwoord:** `20` Pen Testing Labs

#### Vraag 2: HackTheBox Members (10 jun 2017)

**Antwoord:** `1` (alleen de oprichter)

#### Vraag 3: Facebook.com Redirect (maart 2002)

**Antwoord:** `http://www.aboutface.com/`

**Analyse:** Voordat Mark Zuckerberg "TheFacebook" lanceerde, was facebook.com eigendom van een ander bedrijf.

#### Vraag 4: PayPal "Beam Money" Device (okt 1999)

**Antwoord:** `Palm Pilot`

**Analyse:** In de vroege dagen van PayPal (het X.com tijdperk) werd de Palm Pilot PDA gebruikt voor mobile payments.

#### Vraag 5: Google Prototype (nov 1998)

**Antwoord:** `http://google.stanford.edu`

**Analyse:** Het allereerste prototype van Google draaide op de servers van Stanford University.

#### Vraag 6: IANA Last Update (maart 2000)

**Antwoord:** `08-March-2000`

#### Vraag 7: Wikipedia Articles (9 feb 2003)

**Antwoord:** `100000` artikelen (Engels)

---

## Skills Assessment

### Target Informatie

- **IP:** `94.237.59.242`
- **Port:** `54455`
- **Domein:** `inlanefreight.htb`
- **Life Left:** 71 minuten

---

### Vraag 1: IANA ID van inlanefreight.com

**Commando:**

bash

````bash
whois inlanefreight.com
```

**Resultaat:**
```
Registrar: Amazon Registrar, Inc.
Registrar IANA ID: 468
````

**Antwoord:** `468`

---

### Vraag 2: HTTP Server Software

**Commando:**

bash

````bash
curl -I http://94.237.59.242:54455 -H "Host: inlanefreight.htb"
```

**Resultaat:**
```
Server: nginx/1.26.1
````

**Antwoord:** `nginx`

---

### Vraag 3: API Key in Hidden Admin Directory

Dit was de meest complexe vraag die een multi-stage aanpak vereiste.

#### Fase 1: VHost Discovery Probleem

Initiële poging met standaard subdomains faalt:

bash

```bash
for sub in admin dev staging test api internal support www; do
  curl -I http://$sub.inlanefreight.htb:54455
done
```

**Resultaat:** Alle geven `HTTP 000` (DNS resolution failure)

**Root Cause:** Deze subdomains hebben geen DNS records.

---

#### Fase 2: VHost Fuzzing

**Kritiek Inzicht:** We moeten fuzzen met de `Host:` header direct naar het IP:

bash

```bash
gobuster vhost \
  -u http://inlanefreight.htb:59336 \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-20000.txt \
  --append-domain -t 50
```

**Probleem:** Standaard lijst vond alleen bekende vhosts (admin, support, blog)

**Oplossing:** Grotere wordlist (110,000 entries) gebruiken:

bash

```bash
gobuster vhost \
  -u http://inlanefreight.htb:59336 \
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt \
  --append-domain -t 50
```

**Doorbraak:** `web1337.inlanefreight.htb` ontdekt!

---

#### Fase 3: /etc/hosts Update

bash

```bash
echo "94.237.59.242 inlanefreight.htb web1337.inlanefreight.htb" | sudo tee -a /etc/hosts
```

---

#### Fase 4: Content Discovery

**Test of we het juiste subdomein hebben:**

bash

```bash
curl -s http://94.237.59.242:54455/ -H "Host: web1337.inlanefreight.htb"
```

**Output:**

html

```html
<!DOCTYPE html>
<html>
  <head><title>Page 1</title></head>
  <body>
    <h1>Page 1</h1>
    <a href="index-334.html">Next</a>
  </body>
</html>
```

✅ **Bevestiging:** We zitten op het juiste vhost!

---

#### Fase 5: Robots.txt Check

bash

````bash
curl -s http://94.237.59.242:54455/robots.txt -H "Host: web1337.inlanefreight.htb"
```

**Output:**
```
User-agent: *
Allow: /index.html
Allow: /index-2.html
Allow: /index-3.html
Disallow: /admin_h1dd3n
````

🎯 **De Hidden Directory:** `/admin_h1dd3n`

---

#### Fase 6: API Key Extraction

bash

```bash
curl -s http://94.237.59.242:54455/admin_h1dd3n/ -H "Host: web1337.inlanefreight.htb"
```

**Output:**

html

```html
<h1>Welcome to web1337 admin site</h1>
<h2>The admin panel is currently under maintenance, but the API is still 
accessible with the key e963d863ee0e82ba7080fbf558ca0d3f</h2>
```

**Antwoord:** `e963d863ee0e82ba7080fbf558ca0d3f`

---

### Vraag 4: Email Address via Crawling

**Context:** Het subdomein `dev.web1337.inlanefreight.htb` heeft een keten van honderden pagina's (`index-1.html` tot `index-334.html`).

**Automated Crawling Script:**

bash

````bash
for i in {1..500}; do
  echo -n "Checking page $i... "
  curl -s http://94.237.59.242:54455/index-$i.html \
       -H "Host: dev.web1337.inlanefreight.htb" | \
       grep -E -o "\b[A-Za-z0-9._%+-]+@inlanefreight\.htb\b" && break
done
```

**Resultaat:**
```
Checking page 1... Checking page 2... [...] Checking page 80... 
1337testing@inlanefreight.htb
````

**Antwoord:** `1337testing@inlanefreight.htb`

**Gevonden op:** Pagina 80

---

### Vraag 5: Toekomstige API Key

**Hypothese:** Als de e-mail op pagina 80 stond, kunnen developer-comments in dezelfde reeks pagina's zitten.

**Modified Scan:**

bash

```bash
for i in {1..500}; do
  curl -s http://94.237.59.242:54455/index-$i.html \
       -H "Host: dev.web1337.inlanefreight.htb" | \
       grep -iE "API|key|changing|TODO" && echo "Gevonden op pagina $i"
done
```

**Resultaat:**

html

```html
<!-- Remember to change the API key to ba988b835be4aa97d068941dc852ff33 -->
Gevonden op pagina 105
```

**Antwoord:** `ba988b835be4aa97d068941dc852ff33`

**Analyse:** Dit is een klassiek voorbeeld van **Information Disclosure via HTML Comments**. Developers laten TODO's achter met toekomstige credentials.

---

## Geleerde Lessen

### 1. VHost vs Subdomain - Het Verschil Begrijpen

**Subdomain Fuzzing:**

- Gebruikt DNS queries
- Zoekt naar verschillende IP-adressen
- Voorbeeld: `dig admin.site.com`

**VHost Fuzzing:**

- Gebruikt HTTP `Host:` headers
- Hetzelfde IP, verschillende content
- Voorbeeld: `curl -H "Host: admin.site.com" http://192.0.2.1`

**Waarom dit belangrijk is:** In moderne cloud-omgevingen draaien vaak **10-50 verschillende sites** op één IP-adres (cost efficiency). Zonder VHost fuzzing mis je 95% van de attack surface.

---

### 2. Size-Based Filtering is Essentieel

Bij VHost/Directory fuzzing krijg je vaak een "catch-all" response:

bash

````bash
admin.site.com → 200 OK (120 bytes)
dev.site.com   → 200 OK (120 bytes)
test.site.com  → 200 OK (120 bytes)
```

**Zonder filtering:** Je krijgt duizenden false positives.

**Met size filtering (`-fs 120`):** Je ziet alleen de échte vhosts/directories.

---

### 3. Wordlists: Groter is Niet Altijd Beter

**Te klein (common.txt - 4,750 entries):**
- Mist unieke namen zoals `web1337`
- Snelle scans, maar lage coverage

**Te groot (110,000+ entries):**
- Vindt bijna alles
- Zeer tijdrovend (5-15 minuten)

**Sweet Spot (20,000 entries):**
- Balance tussen snelheid en coverage
- Vindt meeste custom names
- ~2-3 minuten scan tijd

---

### 4. De "Cascade Effect" in Reconnaissance

Elke gevonden asset opent nieuwe wegen:
```
1. IP adres gevonden (134.209.24.248)
   ↓
2. Reverse DNS → inlanefreight.com
   ↓
3. VHost scan → web1337.inlanefreight.htb
   ↓
4. robots.txt → /admin_h1dd3n/
   ↓
5. API Key extraction
````

**Les:** Stop nooit na één bevinding. Elke discovery is een starting point.

---

### 5. Developer Artifacts = Goudmijnen

Welke artifacts developers achterlaten:

|Artifact Type|Locatie|Informatie|
|---|---|---|
|**HTML Comments**|`<!-- TODO: ... -->`|Credentials, toekomstige wijzigingen|
|**robots.txt**|`/robots.txt`|Verborgen directories|
|**Error Messages**|Debug mode|Versienummers, file paths|
|**Backup Files**|`config.php.bak`|Credentials in plaintext|
|**Git Repositories**|`/.git/`|Volledige source code|

**Security Best Practice:** Code scrubbing moet deel zijn van de deployment pipeline.

---

### 6. De Kracht van Automation

**Handmatig (1 pagina per keer):**

- 334 pagina's = 334 minuten (5+ uur)
- Foutgevoelig
- Mentaal uitputtend

**Automated (for-loop):**

- 500 pagina's = 2 minuten
- Geen fouten
- Kan parallel draaien

**Conclusie:** Investeer tijd in het leren van bash scripting en tools.

---

### 7. Context is Alles bij OSINT

Een enkele bevinding (zoals een e-mailadres) lijkt onschuldig.

**Maar gecombineerd:**

- E-mail op pagina 80: `1337testing@inlanefreight.htb`
- Comment op pagina 105: Nieuwe API key
- robots.txt: Hidden admin directory

**Wordt het een compleet aanvalsprofiel:**

1. Weten we dat er een dev/test omgeving is
2. Hebben we test-credentials
3. Kennen we de admin toegang
4. Hebben we de toekomstige API architectuur

---

## Tool Arsenal

### DNS Reconnaissance

|Tool|Use Case|Commando Voorbeeld|
|---|---|---|
|**dig**|DNS queries, zone transfers|`dig axfr @ns.site.com site.com`|
|**nslookup**|Simpele DNS lookups|`nslookup site.com`|
|**host**|Reverse lookups|`host 192.0.2.1`|
|**dnsenum**|Automated enumeration|`dnsenum --enum site.com`|
|**fierce**|Subdomain brute-forcing|`fierce --domain site.com`|

---

### VHost & Directory Fuzzing

|Tool|Specialty|Best Voor|
|---|---|---|
|**ffuf**|Snelheid, flexibiliteit|VHosts, directories, parameters|
|**gobuster**|Stabiliteit, gebruiksgemak|Grote wordlists|
|**wfuzz**|Advanced filtering|Complex fuzzing|
|**feroxbuster**|Recursion|Deep directory structures|

---

### Web Crawling

|Tool|Type|Kracht|
|---|---|---|
|**ReconSpider**|Python/Scrapy|Automated extraction|
|**Burp Suite Spider**|Proxy-gebaseerd|In-depth crawling|
|**gospider**|Go-based|Snelheid|
|**hakrawler**|Lightweight|Quick scans|

---

### Fingerprinting

|Tool|Focus|Output|
|---|---|---|
|**WhatWeb**|CMS/Framework detection|Technologie stack|
|**Wappalyzer**|Browser extension|Real-time identification|
|**Nikto**|Vulnerability scanning|Misconfigurations|
|**Nmap**|Service versioning|`nmap -sV`|

---

## Command Reference Sheet

### Quick Reconnaissance Workflow

bash

```bash
# 1. WHOIS Lookup
whois domain.com

# 2. DNS Records
dig domain.com A AAAA MX NS TXT
dig -x IP_ADDRESS  # Reverse

# 3. Zone Transfer Attempt
dig axfr @ns1.domain.com domain.com

# 4. Subdomain Enumeration
dnsenum --enum domain.com -f subdomains.txt

# 5. VHost Discovery
ffuf -w wordlist.txt \
     -u http://IP:PORT/ \
     -H "Host: FUZZ.domain.com" \
     -fs 120

# 6. Banner Grabbing
curl -I http://domain.com

# 7. robots.txt Check
curl http://domain.com/robots.txt

# 8. Directory Fuzzing
ffuf -w /usr/share/seclists/Discovery/Web-Content/common.txt \
     -u http://domain.com/FUZZ

# 9. Email Harvesting
for i in {1..500}; do
  curl -s http://site.com/page-$i.html | \
  grep -Eo "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"
done

# 10. HTML Comment Extraction
curl -s http://site.com | grep -o '<!--.*-->'
```

---

## Security Implications & Remediations

### 1. DNS Zone Transfer Misconfiguratie

**Kwetsbaarheid:** Server staat AXFR toe voor iedereen

**Impact:**

- Volledige infrastructuur mapping
- Identificatie van kritieke targets
- Geen detectie in logs

**Remediation:**

apache

```apache
# BIND9 configuratie
allow-transfer { 
    10.10.34.16;  # DC1
    10.10.34.11;  # DC2
};
```

---

### 2. Information Disclosure via robots.txt

**Kwetsbaarheid:** Gevoelige directories in robots.txt

**Impact:**

- Directe wegwijzer naar admin panels
- Backup directories blootgelegd

**Remediation:**

- Gebruik geen robots.txt voor beveiliging
- Implementeer sterke authenticatie op gevoelige directories
- Gebruik obscurity alleen als extra laag, niet als primaire verdediging

---

### 3. HTML Comment Leakage

**Kwetsbaarheid:** Credentials/API keys in comments

**Impact:**

- Direct credential exposure
- Inzicht in toekomstige architectuur

**Remediation:**

bash

```bash
# Pre-deployment script
find . -type f \( -name "*.html" -o -name "*.php" \) \
  -exec grep -l "<!--.*API\|password\|key.*-->" {} \;
```

**CI/CD Integration:** Automated comment stripping

---

### 4. Predictable VHost Naming

**Kwetsbaarheid:** Subdomeinen volgen voorspelbaar patroon

**Impact:**

- Easy enumeration (web1337, vm5)
- Low security through obscurity

**Remediation:**

- Random strings voor non-public vhosts
- VHost-based authentication
- Separate internal DNS

---

### 5. Developer Artifacts in Production

**Kwetsbaarheid:** Test accounts, TODO's, debug modes

**Impact:**

- Test credentials werken in productie
- Debug info lekt internal paths

**Remediation Checklist:**

- [ ]  Remove all TODO comments
- [ ]  Disable debug modes
- [ ]  Delete test accounts
- [ ]  Strip development artifacts
- [ ]  Use environment-specific configs

---

## Skills Assessment Samenvatting

|Vraag|Onderwerp|Antwoord|Techniek|
|---|---|---|---|
|1|IANA ID|`468`|WHOIS lookup|
|2|HTTP Server|`nginx`|Banner grabbing|
|**3**|**Admin API Key**|`e963d863ee0e82ba7080fbf558ca0d3f`|VHost fuzzing → robots.txt → directory access|
|**4**|**Email**|`1337testing@inlanefreight.htb`|Automated crawling (page 80)|
|**5**|**Future API Key**|`ba988b835be4aa97d068941dc852ff33`|HTML comment extraction (page 105)|

**Totale Score:** 100/100 (5/5 vragen correct)

---

## Tijdlijn van de Assessment

|Tijd|Activiteit|Resultaat|
|---|---|---|
|00:00|Target spawn|IP: 94.237.59.242:54455|
|00:05|WHOIS lookup|IANA ID: 468|
|00:08|Banner grab|Server: nginx|
|00:15|VHost scan (common.txt)|Alleen index.html|
|00:45|VHost scan (20k wordlist)|web1337 ontdekt!|
|01:00|/etc/hosts update|Toegang tot vhost|
|01:05|robots.txt check|/admin_h1dd3n/ gevonden|
|01:10|API key #1 extraction|Vraag 3 ✅|
|01:20|Crawling script page 1-500|Email op p.80 ✅|
|01:25|Comment scan page 1-500|API key op p.105 ✅|
|**01:30**|**Assessment Complete**|**5/5 vragen**|

---

## Aanbevelingen voor Toekomstige Assessments

### Pre-Assessment Checklist

- [ ]  Wordlists gedownload en georganiseerd
- [ ]  /etc/hosts ready voor manual entries
- [ ]  Tools geïnstalleerd (ffuf, gobuster, dig)
- [ ]  Scripting templates klaar (curl loops, grep patterns)
- [ ]  Note-taking systeem actief (Obsidian/CherryTree)

---

### Tijdens Assessment

1. **Begin met Broad Reconnaissance**
    - WHOIS
    - DNS records
    - VHost discovery
2. **Document Everything**
    - Elke bevinding kan later relevant blijken
    - Screenshots van belangrijke outputs
    - Kopieer commando's die werken
3. **Iterative Deepening**
    - Als één methode faalt, probeer alternatieven
    - Grotere wordlists als eerste scan niets oplevert
    - Combine multiple techniques
4. **Time Management**
    - Max 15 min per vraag voor eerste poging
    - Move on en kom later terug
    - Prioritize "easy wins"

---

## Belangrijkste Takeaways

### Technisch

1. **VHost fuzzing is essentieel** in moderne web environments
2. **Size-based filtering** voorkomt false positive overload
3. **Automation** (bash loops, scripts) is onmisbaar voor scalability
4. **Developer artifacts** (comments, robots.txt) zijn vaak de makkelijkste weg naar credentials

### Mindset

1. **Persistence pays off** - de e-mail stond op pagina 80, niet op pagina 1
2. **Every finding is a doorway** - cascade effect in reconnaissance
3. **Think like a developer** - waar laten zij per ongeluk sporen achter?
4. **Automate repetitive tasks** - investeer in scripting skills

### Methodologie

1. **Passief eerst, actief daarna** - vermijd onnodige detectie
2. **Breed eerst, diep daarna** - krijg eerst overview, zoom dan in
3. **Combine multiple sources** - één tool geeft zelden het complete plaatje
4. **Verify findings** - cross-check bevindingen met meerdere methoden

---

## Tags voor Obsidian

#htb #cpts #reconnaissance #information-gathering #web-security #vhost-fuzzing #dns-enumeration #crawling #osint #skills-assessment #penetration-testing #ethical-hacking

## Gerelateerde Notities

- [[DNS Enumeration Techniques]]
- [[Virtual Host Discovery Methods]]
- [[Web Application Reconnaissance]]
- [[OSINT Framework]]
- [[Bash Automation for Pentesters]]
- [[HTB CPTS Certification Path]]

---

**Status:** ✅ Module 100% Voltooid  
**Completion Date:** 25 januari 2026  
**Next Module:** Footprinting / Vulnerability Assessment

---

_Dit rapport is gegenereerd voor gebruik in Obsidian en bevat alle bevindingen, technieken en lessen van de HTB Academy "Information Gathering - Web Edition" module._