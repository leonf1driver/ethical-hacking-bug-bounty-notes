heHarvester -d microsoft.com -b baidu -l 100    of uitgebreider:

theHarvester -d microsoft.com -b baidu,yahoo,duckduckgo,bing -l 100     de honderd zijn het aantal resultaten anders loopt ie vast 

nu met resultaten log :

theHarvester -d microsoft.com -b baidu,yahoo,duckduckgo,bing -l 100 -f resultaten     typ ls voor xml of json log

alle bronnen doorzoeken:

theHarvester -d bol.com -b all -l 100 -f resultaten



De term "harvester commando's" kan op verschillende tools betrekking hebben, maar in de context van **ethisch hacken en verkenning** (waar Shodan, Zmap en Sensys thuishoren), verwijst het vaak naar de tool **TheHarvester**.

TheHarvester is een populaire en veelgebruikte tool die e-mailadressen, subdomeinen, hostnamen, werknamen en banners verzamelt van openbare bronnen (zoals zoekmachines en PGP-servers) om de infrastructuur van een organisatie in kaart te brengen.

Hier zijn de belangrijkste commando's en opties voor TheHarvester:

## 🤖 De Belangrijkste TheHarvester Commando's

TheHarvester wordt uitgevoerd vanaf de commandoregel en gebruikt vlaggen om de zoekparameters te specificeren.

### 1. Basisopbouw van het Commando

De algemene syntaxis is:

Bash

```
theHarvester -d [DOEL DOMEIN] -b [BRON] -l [LIMIET]
```

### 2. Essentiële Vlaggen (Parameters)

|**Vlag**|**Functie**|**Toelichting**|
|---|---|---|
|**`-d`**|**Domain** (Doel)|**Verplicht.** Het domein dat je wilt onderzoeken.|
|**`-b`**|**Source** (Bron)|**Verplicht.** De bron (zoekmachine/database) die je wilt gebruiken.|
|**`-l`**|**Limit** (Limiet)|Het aantal resultaten dat je wilt terugkrijgen (voor Google, Bing, etc.).|
|**`-f`**|**File** (Uitvoer)|Sla de resultaten op in een XML- of JSON-bestand.|

### 3. Beschikbare Bronnen (`-b` optie)

Je moet minstens één bron opgeven met de vlag `-b`. De meest voorkomende bronnen zijn:

- **`google`**: Zoekresultaten van Google.
    
- **`bing`**: Zoekresultaten van Bing.
    
- **`baidu`**: Zoekresultaten van Baidu.
    
- **`linkedin`**: Zoek op LinkedIn voor werknamen.
    
- **`shodan`**: Gebruik Shodan om banners te vinden (vereist een Shodan API-sleutel).
    
- **`hunter`**: Gebruik Hunter.io voor e-mailverificatie (vereist een Hunter API-sleutel).
    
- **`all`**: Gebruik _alle_ beschikbare bronnen (Dit kan lang duren).
    

### 4. Uitgebreide Vlaggen (Advanced Options)

|**Vlag**|**Functie**|**Toelichting**|
|---|---|---|
|**`-e`**|**DNS Bruteforce**|Schakel een DNS-bruteforce aanval in om subdomeinen te vinden.|
|**`-h`**|**Help**|Toont het help-menu met alle beschikbare opties.|
|**`-p`**|**Port Scan**|Voert een basis poortscan uit op de gevonden hosts.|
|**`-s`**|**Start**|Sla de eerste X resultaten over (om zoeklimieten te omzeilen).|
|**`-n`**|**DNS Resolve**|Voer een DNS-resolutie uit op alle gevonden hosts.|

---

## 🛠️ Voorbeelden van TheHarvester Commando's

### 1. Basiszoekopdracht

Vind 500 resultaten voor e-mails en hostnamen voor `voorbeeld.nl` met behulp van Google en Bing:

Bash

```
theHarvester -d voorbeeld.nl -b google,bing -l 500
```

### 2. Uitgebreide Verkenning

Gebruik alle bronnen voor `bedrijf.com` en sla de resultaten op in een XML-bestand:

Bash

```
theHarvester -d bedrijf.com -b all -f bedrijf_scan.xml
```

### 3. Shodan Banners Zoeken

Gebruik Shodan om banners en poorten te vinden (vereist dat je je API-sleutel hebt geconfigureerd):

Bash

```
theHarvester -d domeinnaam.org -b shodan
```
