
Email adres 1:  👉 **`leonkeijzers+ford@hotmail.com `**    NACleon1%
Email adres 2:         leonkeijzers+ford2@hotmail.com         MAXverstappen1%

IDOR niet gevonden snelle test

Hier is het antwoord op je twee vragen.

### 1. Hoe lang heb je nog een Pro-account bij mij?

Dat kan ik helaas **niet zien**. Ik ben een AI-model en heb uit privacy- en veiligheidsoverwegingen geen toegang tot jouw persoonlijke accountgegevens, betaalgegevens of abonnementen (zoals Google One of Gemini Advanced).

Je kunt dit zelf checken door naar **[myaccount.google.com](https://myaccount.google.com)** te gaan en te klikken op "Betalingen en abonnementen".  19 december dus

---

### 2. De Lijst met Legale Nederlandse Doelwitten 🇳🇱 (Responsible Disclosure)

Er is geen enkele "complete lijst" die altijd klopt (omdat beleid verandert), maar hier zijn de **grote categorieën en bedrijven** waarvan bekend is dat ze een Responsible Disclosure (CVD) beleid hebben.

⚠️ **Belangrijk:** Zoek altijd éérst op Google naar `"[Bedrijfsnaam] responsible disclosure"` voordat je Burp aanzet. De regels kunnen veranderd zijn.

#### 🏛️ Overheid & Publieke Sector (Veiligste keuze)

Bijna de hele Rijksoverheid doet mee aan CVD. Geen geld, wel een T-shirt en eer.

- **Rijksoverheid:** `rijksoverheid.nl`, `belastingdienst.nl`, `rdw.nl` (RDW), `defensie.nl`.
    
- **Gemeenten:** Veel grote gemeenten (Amsterdam, Den Haag, Rotterdam) hebben een beleid.
    
- **Universiteiten:** TUDelft, Universiteit Utrecht, HvA (Check hun specifieke beleid!).
    

#### 📡 Telecom & Internet (Grote Scope)

- **KPN:** (Inclusief merken als Simyo, XS4ALL). Heel professioneel team.
    
- **VodafoneZiggo:** (`vodafone.nl`, `ziggo.nl`).
    
- **Odido:** (Vroeger T-Mobile).
    
- **SIDN:** De beheerder van alle `.nl` domeinen.
    

#### 🛒 Retail & E-commerce (Jouw favoriet)

- **Coolblue:** (`coolblue.nl`). Hebben we al besproken. Mailen naar security.
    
- **Bol.com:** (`bol.com`). Via HackerOne of eigen beleid.
    
- **Ahold Delhaize:** Moederbedrijf van **Albert Heijn** (`ah.nl`), **Etos**, **Gall & Gall**. (Zoek naar "Ahold Delhaize Responsible Disclosure").
    
- **Jumbo:** (`jumbo.com`).
    

#### 🏦 Banken & Financiën (Moeilijkheidsgraad: Expert)

_Pas op: Banken zijn extreem streng. Doe hier geen gekke dingen._

- **Volksbank:** (SNS, ASN, RegioBank).
    
- **Bunq:** (Hacker-vriendelijke bank, zit op HackerOne).
    
- **Rabobank:** Heeft een beleid, maar is streng.
    
- **ABN AMRO:** Heeft een beleid.
    

#### ✈️ Reizen & Vervoer

- **Transavia:** (`transavia.com`).
    
- **Schiphol:** (`schiphol.nl`).
    
- **NS:** (Nederlandse Spoorwegen).
    

---

### 💡 De "Gouden Tip" voor nog meer targets: Zerocopter 🚁

Veel Nederlandse bedrijven gebruiken het platform **Zerocopter** om hun meldingen te beheren. Als je op hun site kijkt, zie je vaak een lijst met klanten of een formulier dat naar Zerocopter leidt.

- _Voorbeelden:_ Postcode Loterij, Swapfiets, SnappCar.
    

### 📝 Huiswerk

Kies één categorie (bijv. "Retail" of "Overheid") en zoek van 3 bedrijven hun `security.txt` op. Sla die op in je Obsidian onder `Targets`.

Zullen we teruggaan naar **Coolblue** om die IDOR te testen? 🍊

TWEEDE BUG BOUNTY:

marktplaats en coolblue doen 
Als Bol.com te moeilijk is, hier zijn makkelijkere opties:

### **1. 🥈 Coolblue**

```
Target: coolblue.nl
Type: E-commerce (tech)
Bounties: €25-100 geschat
Features: Product reviews, wishlists, orders
IDOR kans: Hoog (veel user data)
```

### **2. 🥉 Marktplaats**

```
Target: marktplaats.nl
Type: Classifieds
Bounties: Mogelijk
Features: Listings, messaging, favorites
IDOR kans: Zeer hoog (user listings)
```

### **3. 🏦 Tikkie (ABN AMRO)**

```
Target: tikkie.me
Type: Payment requests
Bounties: €100-500+ (financial)
IDOR kans: Critical if found!
```

## Waarom FanDuel VDP goed is voor jou:

✅ **Zeer nieuw programma** - Gelanceerd in November 2025 (deze maand!) ✅ **Weinig competitie** - Pas 36 reports in 90 dagen ontvangen ✅ **Hoge response rate** - 100% ✅ **Triaged door HackerOne** - Professionals beoordelen je reports ✅ **Duidelijke regels** - Zeer gedetailleerde policy

## Wat is IN SCOPE (waar je naar moet zoeken):

### **Kwetsbaarheden die ze accepteren:**

1. **Cross-Site Scripting (XSS)** ⭐ Goed voor beginners
2. **Cross-Site Request Forgery (CSRF)**
3. **Insecure Direct Object References (IDOR)** ⭐ Vaak te vinden
4. **Injection Vulnerabilities** (SQL injection, etc.)
5. **Authentication Vulnerabilities** ⭐ Check login/registratie flows
6. **Server-Side Code Execution**
7. **Privilege Escalation**
8. **Security Misconfiguration**
9. **Directory Traversal**
10. **Information Disclosure**

### **Assets (waar je mag testen):**

- fanduel.com en subdomains
- FanDuel mobile apps
- Gerelateerde services

## Wat is OUT OF SCOPE (niet rapporteren):

❌ **Veel voorkomende false positives:**

- Clickjacking zonder sensitive actions
- CSRF op publieke forms
- Open redirects (tenzij je extra impact kan tonen)
- Missing security headers (HSTS, CSP, etc.)
- SSL/TLS configuratie issues
- Descriptive error messages
- Banner disclosure
- Rate limiting issues
- Outdated browser issues
- Username enumeration op `*.fdbox.net/users/[userId]`

## BELANGRIJKE REGELS:

### ✅ **Wel doen:**

1. Gebruik je eigen test account (maak een FanDuel account aan)
2. Gebruik je HackerOne email (@wearehackerone.com) bij registratie
3. Voeg deze header toe aan al je requests: `X-HackerOne: [jouw_username]`
4. Stop METEEN als je persoonlijke data (PII) tegenkomt
5. Geef duidelijke reproduceerbare stappen
6. Test alleen het minimum om de vulnerability te bewijzen

### ❌ **Niet doen:**

- Geen automated scanners die veel traffic genereren (bijv. 50.000 requests in 2 minuten)
- Geen data exfiltreren
- Geen DDoS/DoS aanvallen
- Geen social engineering
- Geen brute force attacks
- Niet testen als je onder 18 bent

## HOE TE BEGINNEN:

### **Stap 1: Account aanmaken**

```
1. Ga naar fanduel.com
2. Maak een account met je HackerOne email
3. Verifieer je account
```

### **Stap 2: Basis reconnaissance**

```
1. Bekijk fanduel.com in je browser
2. Open Developer Tools (F12)
3. Navigeer door de site en bekijk:
   - Cookies
   - Local storage
   - API calls in Network tab
```

### **Stap 3: Zoek naar makkelijke bugs** (voor beginners):

**A) IDOR (Insecure Direct Object Reference)**

```
- Kijk naar URLs met IDs: /profile/12345, /bet/67890
- Probeer andermans ID in te vullen (van je tweede test account)
- Kun je data zien/wijzigen die niet van jou is?
```

**B) XSS (Cross-Site Scripting)**

```
- Zoek input velden (naam, comments, bio, etc.)
- Test met: <script>alert(1)</script>
- Als het niet werkt, probeer: <img src=x onerror=alert(1)>
```

**C) Authentication issues**

```
- Kun je zonder login bij protected pages?
- Reset password flow - kun je anderen hun wachtwoord resetten?
- Email verification - wordt het gecheckt?
```

### **Stap 4: Report schrijven**

Gebruik hun Report Assistant en structureer zo:

```
Title: [Vulnerability Type] in [Location]
Bijv: "IDOR in user profile allows access to other users' data"

Description:
1. Wat is het probleem?
2. Waar heb je het gevonden?
3. Wat is de impact?

Steps to Reproduce:
1. Login as user A
2. Navigate to /profile/123
3. Change ID to /profile/456 (user B's ID)
4. Observe: You can see user B's private data

Impact:
Privacy violation - any user can view other users' personal information

Proof of Concept:
[Screenshot of het probleem]
[HTTP request/response if relevant]
```

## PRAKTISCHE TIPS:

1. **Begin simpel**: Focus eerst op 1-2 vulnerability types
2. **Gebruik tools**:
    - Browser Developer Tools (F12)
    - Burp Suite Community (gratis)
    - OWASP ZAP (gratis)
3. **Maak 2 test accounts** om IDOR te testen
4. **Voeg altijd header toe**: `X-HackerOne: jouw_username`
5. **Stop bij PII**: Als je echte user data ziet, stop en report meteen

## LET OP:

⚠️ **Geen betaling** - Dit is een VDP (geen bounty) ⚠️ **Test responsibly** - Geen excessive traffic ⚠️ **Report quality matters** - Duidelijke reproduceerbare stappen

Wil je dat ik je help met:

1. Hoe je Burp Suite moet instellen voor FanDuel?
2. Specifieke test scenarios voor IDOR/XSS?
3. Voorbeelden van goede bug reports?

Veel succes! 🎯

L

NUCLEI,NMAP,GOBUSTEREN MEER WELKE WEL WELKE NIET

Goede vraag! Laat me je precies vertellen welke tools je WEL en NIET mag gebruiken bij FanDuel, gebaseerd op hun policy:

## ❌ ABSOLUUT NIET TOEGESTAAN:

### **NUCLEI** ❌

```
NIET GEBRUIKEN - Te agressief!
```

**Waarom niet?**

- Nuclei stuurt duizenden requests in korte tijd
- FanDuel policy zegt: "50,000 requests in 2 minutes is excessive"
- Nuclei doet vaak meer dan dit
- **Risico:** Account ban + IP block

### **NMAP** ⚠️ ZEER BEPERKT

```
ALLEEN: Eén enkele scan tegen één host
NIET: Aggressive scans, meerdere hosts, port scanning van hele ranges
```

**Van hun policy:**

> "running one nmap scan against one host is allowed"

**Veilig gebruik:**

bash

````bash
# ✅ OK - Basic scan van 1 host
nmap fanduel.com

# ✅ OK - Service detection op 1 host  
nmap -sV fanduel.com -p 80,443

# ❌ NIET OK - Te agressief
nmap -A -T5 fanduel.com
nmap 10.0.0.0/24
nmap -p- fanduel.com  # Alle ports scannen
```

### **GOBUSTER** ❌ NIET TOEGESTAAN
```
NIET GEBRUIKEN - Te veel traffic!
```
**Waarom niet?**
- Gobuster stuurt honderden/duizenden requests per seconde
- Dit valt onder "excessive traffic"
- Directory brute forcing is eigenlijk fuzzing = te veel requests

## ✅ WEL TOEGESTAAN (met mate):

### **1. BURP SUITE** ✅ (MET BEPERKINGEN)
```
✅ Manual testing - Perfect
✅ Proxy/Intercept - Perfect  
✅ Repeater - Perfect
⚠️ Intruder - ALLEEN met kleine wordlists en slow speed
❌ Scanner (Pro) - TE AGRESSIEF
```

**Veilig Burp gebruik:**
```
Intruder settings:
- Max: 1-2 threads
- Delay tussen requests: 500-1000ms
- Kleine wordlist: max 100-200 items
```

### **2. OWASP ZAP** ✅ (PASSIVE MODE)
```
✅ Passive scanning - Perfect
✅ Manual explore - Perfect
✅ Proxy - Perfect
❌ Active scan - TE AGRESSIEF
❌ Spider - Te veel requests
```

### **3. BROWSER DEVELOPER TOOLS** ✅
```
✅ Network tab - PERFECT voor beginners
✅ Console - Perfect
✅ Application/Storage - Perfect
✅ Sources - Perfect
```

### **4. CURL / WGET** ✅
```
✅ Manual requests - Perfect
✅ Testing specific endpoints - Perfect
````

**Voorbeeld:**

bash

````bash
# ✅ OK - Enkele requests met jouw header
curl -H "X-HackerOne: jouw_username" https://fanduel.com/api/user/123

# ✅ OK - Testing IDOR manually
curl -H "X-HackerOne: jouw_username" \
     -H "Cookie: session=xyz" \
     https://fanduel.com/api/profile/456
```

### **5. POSTMAN** ✅
```
✅ Manual API testing - Perfect
✅ Creating collections - Perfect
✅ Testing authentication - Perfect
```

### **6. WFUZZ** ❌ NIET TOEGESTAAN
```
❌ Te agressief - Fuzzing tool = te veel requests
```

### **7. FFUF** ❌ NIET TOEGESTAAN
```
❌ Directory/file fuzzing = te veel requests
```

### **8. SQLMAP** ⚠️ ZEER BEPERKT
```
⚠️ ALLEEN manual, single payload testing
❌ NIET: Automated scanning mode
````

**Als je SQL injection wilt testen:**

bash

````bash
# ❌ NIET OK - Automated
sqlmap -u "https://fanduel.com/page?id=1" --batch

# ✅ OK - Manual single payloads via Burp/Curl
curl "https://fanduel.com/page?id=1' OR '1'='1"
```

## 📋 COMPLETE TOOL LIJST:

| Tool | Status | Reden |
|------|--------|-------|
| **Nuclei** | ❌ | Te veel automated requests |
| **Nmap** | ⚠️ | Alleen 1 host, basic scan |
| **Gobuster** | ❌ | Directory brute force = te veel traffic |
| **Dirbuster** | ❌ | Directory brute force = te veel traffic |
| **Ffuf** | ❌ | Fuzzing = te veel traffic |
| **Wfuzz** | ❌ | Fuzzing = te veel traffic |
| **Burp Suite** | ✅ | Manual + Repeater OK, Intruder voorzichtig |
| **OWASP ZAP** | ✅ | Passive mode only |
| **Browser DevTools** | ✅ | Perfect! |
| **Curl/Wget** | ✅ | Perfect! |
| **Postman** | ✅ | Perfect! |
| **SQLMap** | ⚠️ | Alleen manual payloads |
| **Subfinder** | ⚠️ | Passive subdomain enum OK |
| **Amass** | ⚠️ | Alleen passive mode |
| **Sublist3r** | ⚠️ | Passive OK |
| **Nikto** | ❌ | Web scanner = te agressief |
| **Nessus** | ❌ | Vulnerability scanner = te agressief |

## 🎯 MIJN AANBEVELING VOOR BEGINNERS:

### **Start met deze tools (100% veilig):**

1. **Browser Developer Tools (F12)**
   - Network tab voor API calls
   - Console voor JavaScript testing
   - Storage voor cookies/tokens

2. **Burp Suite Community - MANUAL mode**
   - Proxy om traffic te zien
   - Repeater om requests te herhalen
   - Decoder voor base64/URL encoding

3. **Curl/Postman**
   - Manual API testing
   - Custom headers toevoegen

### **ALTIJD DEZE HEADER TOEVOEGEN:**
```
X-HackerOne: jouw_username
```

## ⚠️ WAARSCHUWING VAN FANDUEL:

Van hun policy:
> "Large-scale vulnerability scanners, scrapers, or automated tools which produce excessive amounts of traffic are not permitted. Failure to comply may result in suspension of your FanDuel account and a ban of your IP address."

> "sending 50,000 requests in two minutes using Burp Suite Intruder is excessive"

## ✅ VEILIGE WORKFLOW VOOR BEGINNERS:
```
1. Browse de site normaal (met F12 open)
2. Kijk naar:
   - API endpoints in Network tab
   - Cookies en tokens
   - URL parameters met IDs

3. Test manual met Burp Repeater:
   - IDOR: verander IDs
   - XSS: test input fields
   - Auth bypass: probeer endpoints zonder login

4. Document alles
5. Submit report met duidelijke stappen
````

## 🎓 PRAKTISCH VOORBEELD - IDOR VINDEN ZONDER SCANNERS:

bash

```bash
# Stap 1: Login in browser, open F12
# Stap 2: Ga naar je profile
# Stap 3: Zie in Network tab:
GET /api/user/12345 HTTP/1.1

# Stap 4: Kopieer als cURL
# Stap 5: Verander ID manually
curl -H "X-HackerOne: jouw_username" \
     -H "Cookie: session=abc123" \
     https://fanduel.com/api/user/67890

# Stap 6: Zie je andere user data? = IDOR bug!
```

**Geen scanners nodig! Dit is manual testing en 100% toegestaan.**

## 📌 SAMENVATTING:

**Gebruik ALLEEN:**

- ✅ Browser DevTools
- ✅ Burp Suite (manual/repeater)
- ✅ Curl/Postman
- ✅ Manual testing

**NIET gebruiken:**

- ❌ Nuclei
- ❌ Gobuster/Dirbuster/Ffuf
- ❌ Nikto
- ❌ Automated scanners
- ❌ Anything dat 1000+ requests/minuut doet

Wil je dat ik je laat zien hoe je Burp Suite instelt voor veilig manual testing?

L

geef een super comlete uitleg over alles wat ik moet doen met alle tools en wat als eerste doen

# 🎯 COMPLETE BEGINNER GUIDE: BUG BOUNTY OP FANDUEL VDP

Ik ga je STAP-VOOR-STAP door het hele proces leiden, van nul tot je eerste bug report!

---

## 📚 FASE 1: VOORBEREIDING (30-60 minuten)

### **STAP 1: Maak accounts aan**

#### A) HackerOne Account

```
1. Ga naar: https://hackerone.com/sign_up
2. Registreer met een email
3. Verifieer je email
4. Vul je profiel in
5. BELANGRIJK: Noteer je HackerOne username!
```

#### B) FanDuel Test Account

```
1. Ga naar: https://fanduel.com
2. Klik "Sign Up"
3. Gebruik je HackerOne email: [username]@wearehackerone.com
   (Dit krijg je automatisch van HackerOne)
4. Vul valide gegevens in (eigen naam/adres)
5. Verifieer je account
6. Login en kijk rond (5 minuten)
```

#### C) Tweede Test Account (voor IDOR testing)

```
1. Gebruik een ander email adres
2. Registreer nog een FanDuel account
3. Dit gebruik je om te testen of je data van account A 
   kunt zien/wijzigen vanuit account B
```

### **STAP 2: Installeer tools**

#### A) Browser (gebruik Chrome of Firefox)

```
Je hebt al een browser, maar check:
- Developer Tools werken (druk F12)
- Cookies zijn enabled
```

#### B) Burp Suite Community (GRATIS)

```
1. Download van: https://portswigger.net/burp/communitydownload
2. Installeer (volg wizard)
3. Start Burp Suite
4. Kies "Temporary project" > "Use Burp defaults" > Start
```

#### C) Optioneel maar handig:

- **Postman**: [https://www.postman.com/downloads/](https://www.postman.com/downloads/)
- **Notepad++** of **VSCode**: voor notities

---

## 🔧 FASE 2: SETUP TOOLS (30 minuten)

### **BURP SUITE CONFIGURATIE**

#### Stap 1: Start Burp Suite

```
1. Open Burp Suite
2. Kies "Temporary project"
3. Klik "Start Burp"
4. Ga naar tab "Proxy" > "Intercept"
5. Zorg dat "Intercept is off" staat (klik op knop om uit te zetten)
```

#### Stap 2: Browser configureren voor Burp

```
CHROME/FIREFOX CONFIGURATIE:

1. In Burp: Ga naar Proxy > Proxy settings
2. Zie dat het luistert op: 127.0.0.1:8080

CHROME:
3. Download extensie: "FoxyProxy"
4. Configureer proxy:
   - Proxy Type: HTTP
   - Proxy IP: 127.0.0.1
   - Port: 8080
5. Enable de proxy

FIREFOX:
3. Settings > Network Settings > Manual proxy
4. HTTP Proxy: 127.0.0.1
5. Port: 8080
6. Check: "Use this proxy for HTTPS"
7. OK
```

#### Stap 3: Installeer Burp CA Certificate

```
1. Met proxy AAN, ga naar: http://burpsuite
2. Klik "CA Certificate" (rechtsboven)
3. Download het certificate

CHROME:
4. Settings > Privacy and Security > Security
5. Manage certificates > Authorities > Import
6. Selecteer downloaded certificate
7. Check "Trust this certificate for identifying websites"

FIREFOX:
4. Settings > Privacy & Security > Certificates > View Certificates
5. Authorities > Import
6. Selecteer certificate
7. Check "Trust this CA to identify websites"
```

#### Stap 4: Test of het werkt

```
1. In Burp: Tab "Proxy" > "HTTP history"
2. Browser: Ga naar https://fanduel.com
3. In Burp: Zie je requests verschijnen? ✅
4. Zo niet: Herhaal configuratie
```

---

## 🎯 FASE 3: RECONNAISSANCE (1-2 uur)

### **STAP 1: Passive Reconnaissance**

#### A) Browse de site normaal (20 min)

```
Doel: Begrijp hoe de site werkt

1. Login met Account 1
2. Open Developer Tools (F12)
3. Ga naar tab "Network"
4. Klik "Preserve log"

Verken deze functionaliteit:
- ✅ Profile/Account settings
- ✅ Betting functionality (als beschikbaar)
- ✅ Payment/wallet section
- ✅ Messages/notifications
- ✅ Settings
- ✅ Any social features
- ✅ Search functionality
- ✅ Upload features (avatar, etc)

Let op in Network tab:
- API endpoints
- Parameters in URLs (?id=123, ?user=abc)
- POST requests (form submissions)
```

#### B) Document API Endpoints (30 min)

```
Open een notitiebestand en noteer ALLES:

FORMAT:
Endpoint: /api/user/profile
Method: GET
Parameters: userId
Authentication: Cookie-based
Purpose: Get user profile data

VOORBEELDEN DIE JE WAARSCHIJNLIJK ZIET:
- /api/user/{id}
- /api/profile
- /api/account/settings
- /api/bets
- /api/transactions
- /api/notifications

Voor elk endpoint noteer:
1. URL
2. Method (GET/POST/PUT/DELETE)
3. Parameters
4. Headers (vooral Authorization/Cookie)
5. Response type
```

#### C) Analyseer in Burp Suite (20 min)

```
1. Burp > Proxy > HTTP History
2. Filter op: fanduel.com (rechtermuisklik > "Add to scope")
3. Kijk naar interessante requests:

ZOEK NAAR:
✓ Requests met IDs in URL: /user/12345
✓ API calls naar /api/*
✓ POST requests (form submissions)
✓ Requests met tokens/session cookies
✓ File uploads
✓ Search/filter parameters

MARKEER INTERESSANTE REQUESTS:
- Rechtermuisklik > "Highlight" > Kies kleur
- Add comments: rechtermuisklik > "Add comment"
```

### **STAP 2: Identify Attack Surface**

#### Maak een lijst van testpunten:

```
PRIORITEIT 1 (Makkelijk voor beginners):
□ Profile page met user ID in URL
□ API endpoints met IDs
□ Input fields (naam, bio, comments)
□ File upload (avatar, documents)
□ Search functionality
□ Password reset flow

PRIORITEIT 2 (Iets moeilijker):
□ Payment endpoints
□ Admin/moderator functions
□ Multi-step processes
□ Session management
□ Rate limiting
```

---

## 🐛 FASE 4: VULNERABILITY TESTING (Hier vind je bugs!)

### **TEST 1: IDOR (Insecure Direct Object Reference)** ⭐ BESTE VOOR BEGINNERS

#### Wat is IDOR?

```
Je kunt data van andere users zien/wijzigen door IDs te veranderen.

Voorbeeld:
- Jij: /api/user/123 (jouw profiel)
- Test: /api/user/124 (andermans profiel)
- BUG: Je ziet profiel van user 124!
```

#### Hoe te testen:

```
STAP 1: Find endpoints met IDs
-------
Login met Account 1
Ga naar je profile
Zie in Network tab of URL: /profile/12345

STAP 2: Intercept met Burp
-------
1. Burp > Proxy > Intercept ON
2. Browser: Refresh je profile page
3. In Burp: Zie je de request?
4. Request ziet er zo uit:

   GET /api/user/12345 HTTP/1.1
   Host: fanduel.com
   Cookie: session=abc123
   X-HackerOne: jouw_username

STAP 3: Send to Repeater
-------
1. Rechtermuisklik op request
2. "Send to Repeater"
3. Ga naar tab "Repeater"

STAP 4: Test IDOR
-------
1. In Repeater: Verander 12345 naar 12346
2. Klik "Send"
3. Kijk naar Response:

   ✅ BUG GEVONDEN als je ziet:
   - Andere user's email
   - Andere user's private info
   - Data die niet van jou is

   ❌ Geen bug als:
   - 401 Unauthorized
   - 403 Forbidden  
   - Empty response
   - Generic error

STAP 5: Verify met je tweede account
-------
1. Login met Account 2
2. Ga naar profile
3. Noteer het user ID (bijv: 67890)
4. Terug naar Account 1
5. In Burp Repeater: gebruik ID 67890
6. Zie je Account 2's data? = CONFIRMED BUG!
```

#### IDOR Checklist:

```
Test deze endpoints:
□ /api/user/{id}
□ /api/profile/{id}
□ /profile?user={id}
□ /account/{id}
□ /bets/{id}
□ /transactions/{id}
□ /messages/{id}
□ /notifications/{id}

Test deze methods:
□ GET (kan je zien?)
□ PUT (kan je wijzigen?)
□ DELETE (kan je verwijderen?)
□ POST (kan je maken voor anderen?)
```

---

### **TEST 2: XSS (Cross-Site Scripting)** ⭐ GOED VOOR BEGINNERS

#### Wat is XSS?

```
Je kunt JavaScript code injecteren die wordt uitgevoerd
in de browser van andere users.
```

#### Hoe te testen:

```
STAP 1: Find input fields
-------
Zoek overal waar je tekst kunt invoeren:
- Profile name/bio
- Comments
- Search box
- Messages
- Support tickets
- Any text field

STAP 2: Basic XSS test
-------
1. In een input field, type:
   <script>alert(1)</script>

2. Submit/Save

3. Refresh de page

4. Zie je een alert popup? = XSS BUG!

STAP 3: Als basic niet werkt, try variations
-------
Test deze payloads (één per keer):

A) Image based:
<img src=x onerror=alert(1)>

B) SVG based:
<svg onload=alert(1)>

C) Event handler:
<body onload=alert(1)>

D) Input based:
"><script>alert(1)</script>

E) Uppercase (bypass filters):
<SCRIPT>alert(1)</SCRIPT>

F) Without brackets:
javascript:alert(1)

G) In attributes:
" onmouseover="alert(1)

STAP 4: Check waar het verschijnt
-------
Stored XSS (ernstiger):
- Payload blijft opgeslagen
- Andere users zien het ook
- Test: Login met Account 2, zie je de alert?

Reflected XSS:
- Payload zit in URL
- Alleen jij ziet het (direct)
- Kan nog steeds gevaarlijk zijn
```

#### XSS Checklist:

```
Test deze locaties:
□ Profile name
□ Profile bio/description
□ Username
□ Comment sections
□ Search queries (check URL parameters)
□ Error messages
□ Contact/support forms
□ File upload names
□ Any user-controllable text

Test deze contexts:
□ Inside HTML tags
□ Inside HTML attributes
□ Inside JavaScript
□ Inside CSS
```

---

### **TEST 3: Authentication Bypass** ⭐ MEDIUM MOEILIJKHEID

#### Hoe te testen:

```
STAP 1: Find protected pages
-------
1. Login met Account 1
2. Navigate naar: /account/settings (example)
3. In Burp HTTP History: Find dit request
4. Send to Repeater

STAP 2: Test zonder authentication
-------
1. In Repeater: Verwijder Cookie header
2. Klik "Send"
3. Krijg je nog steeds de pagina? = BUG!

STAP 3: Test met expired/invalid session
-------
1. In Repeater: Verander cookie value
   Van: session=abc123
   Naar: session=invalid123
2. Klik "Send"
3. Krijg je de pagina? = BUG!

STAP 4: Test direct object access
-------
Try to access admin/premium features:
- /admin
- /api/admin
- /moderator
- /premium
- /internal

In Burp Repeater:
GET /admin HTTP/1.1
Host: fanduel.com
X-HackerOne: jouw_username
```

#### Authentication Checklist:

```
□ Can you access /account without login?
□ Can you access /admin?
□ Does password reset work without token?
□ Can you reuse old session tokens?
□ Can you access API without auth?
□ Are there unprotected API endpoints?
□ Can you bypass 2FA?
```

---

### **TEST 4: Password Reset Vulnerabilities** ⭐ GOED VOOR BEGINNERS

```
STAP 1: Start password reset
-------
1. Logout
2. Click "Forgot Password"
3. Enter Account 1 email
4. Open Developer Tools > Network tab
5. Submit

STAP 2: Analyze the flow
-------
Check voor deze bugs:

A) Token in URL is guessable:
   /reset?token=12345
   Try: /reset?token=12346
   Can you reset someone else's password?

B) Token doesn't expire:
   Use old reset link
   Does it still work hours/days later? = BUG!

C) Token reuse:
   Use same reset link twice
   Does it work? = BUG!

D) Host header injection:
   In Burp Repeater:
   POST /forgot-password HTTP/1.1
   Host: evil.com
   
   Does email contain evil.com link? = BUG!

E) No rate limiting:
   Request password reset 100 times
   All emails sent? = BUG!
```

---

## 📝 FASE 5: DOCUMENTING & REPORTING

### **Als je een bug hebt gevonden:**

```
STAP 1: Verify de bug
-------
1. Test het 3x om zeker te zijn
2. Test met verschillende accounts
3. Screenshot elk stap
4. Save HTTP requests/responses

STAP 2: Determine impact
-------
Vraag jezelf af:
- Wat kan een attacker hiermee doen?
- Hoeveel users zijn affected?
- Kan dit leiden tot data breach?
- Is er financial impact?

STAP 3: Gather evidence
-------
Maak screenshots van:
✓ Initial state (before exploit)
✓ Exploit steps
✓ Final result (after exploit)
✓ Burp requests/responses
✓ Browser developer tools

STAP 4: Write the report
-------
```

### **REPORT TEMPLATE**

markdown

````markdown
# [Vulnerability Type] in [Location]

## Summary
[1-2 zinnen wat de bug is]

Voorbeeld:
"An Insecure Direct Object Reference (IDOR) vulnerability exists in the user profile API endpoint, allowing any authenticated user to view private information of other users by manipulating the user ID parameter."

## Description
[Uitgebreide beschrijving]

The application's /api/user/{id} endpoint does not properly validate whether the authenticated user has permission to access the requested user profile. By changing the user ID in the API request, I was able to access private profile information of other users including their email addresses, phone numbers, and account settings.

## Steps To Reproduce

### Setup:
1. Create two test accounts:
   - Account A: user123@wearehackerone.com (ID: 12345)
   - Account B: user456@wearehackerone.com (ID: 67890)

### Exploitation Steps:
1. Login to FanDuel with Account A
2. Navigate to your profile page at https://fanduel.com/profile
3. Open Browser Developer Tools (F12) and go to Network tab
4. Observe the API call: `GET /api/user/12345`
5. In Burp Suite Repeater, modify the request:
```
   GET /api/user/67890 HTTP/1.1
   Host: api.fanduel.com
   Cookie: session=AccountA_session_token
   X-HackerOne: your_username
```
6. Send the request
7. Observe: The response contains Account B's private information

### Expected Result:
The API should return 403 Forbidden or similar error.

### Actual Result:
The API returns Account B's full profile data including:
- Email address
- Phone number
- Date of birth
- Account settings
- Transaction history

## Impact

**Severity: High**

This vulnerability allows any authenticated user to:
- Access private information of ALL users on the platform
- Enumerate user data by incrementing user IDs
- Potentially scrape entire user database
- Violate user privacy at scale

This could lead to:
- Privacy violations (GDPR/CCPA concerns)
- Identity theft
- Targeted phishing attacks
- Reputational damage to FanDuel

## Proof of Concept

### HTTP Request:
```http
GET /api/user/67890 HTTP/1.1
Host: api.fanduel.com
Cookie: session=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
X-HackerOne: your_username
User-Agent: Mozilla/5.0...
```

### HTTP Response:
```json
{
  "userId": 67890,
  "email": "victim@example.com",
  "phone": "+1234567890",
  "dateOfBirth": "1990-01-01",
  "accountBalance": 1234.56,
  "kycStatus": "verified"
}
```

### Screenshots:
[Attach screenshots here]

1. account_a_profile.png - Logged in as Account A
2. burp_request.png - Modified request in Burp Suite
3. burp_response.png - Response showing Account B's data
4. account_b_verification.png - Verified data matches Account B

## Remediation

Implement proper authorization checks:
```python
# Current (vulnerable):
def get_user_profile(user_id):
    return User.query.get(user_id)

# Fixed (secure):
def get_user_profile(user_id, current_user_id):
    if user_id != current_user_id:
        raise Forbidden("Cannot access other users' profiles")
    return User.query.get(user_id)
```

Additionally:
1. Implement attribute-based access control (ABAC)
2. Add audit logging for profile access attempts
3. Consider using UUIDs instead of sequential IDs
4. Implement rate limiting on profile endpoints

## References
- OWASP Top 10: A01:2021 – Broken Access Control
- CWE-639: Authorization Bypass Through User-Controlled Key

---

**Tested on:**
- Date: 2025-11-28
- Platform: FanDuel Web Application
- Browser: Chrome 120.0.0.0
- Account: [your_username]@wearehackerone.com
```

---

## 🎯 FASE 6: SUBMIT REPORT
```
STAP 1: Go to HackerOne
-------
1. https://hackerone.com/fanduel-vdp
2. Click "Submit report"
3. Optioneel: Use "Report Assistant" (recommended)

STAP 2: Fill in details
-------
1. Title: Copy from template
2. Vulnerability Information:
   - Type: [Select from dropdown]
   - Severity: [Your assessment]
   - Weakness: [Select CWE]
3. Description: Paste your report
4. Attachments: Upload screenshots
5. Add HTTP requests in "Supporting Material"

STAP 3: Review & Submit
-------
1. Read alles nog een keer
2. Check dat X-HackerOne header overal in staat
3. Verify screenshots zijn duidelijk
4. Submit!

STAP 4: Wait for response
-------
- First Response: 5-10 business days
- Triage: 5-10 business days
- Be patient!
- They might ask questions - respond quickly
```

---

## 📊 PRIORITEIT SCHEMA (Wat eerst te testen)

### **DAG 1: Setup & Reconnaissance (4-6 uur)**
```
✓ Install tools (1 uur)
✓ Configure Burp Suite (30 min)
✓ Create accounts (30 min)
✓ Browse & document (2 uur)
✓ Identify test targets (1 uur)
```

### **DAG 2-3: IDOR Testing (4-8 uur)**
```
✓ Test alle endpoints met IDs
✓ Test met beide accounts
✓ Document findings
✓ Write report als je bug vindt
```

### **DAG 4-5: XSS Testing (4-8 uur)**
```
✓ Test alle input fields
✓ Try verschillende payloads
✓ Test stored vs reflected
✓ Document & report
```

### **DAG 6-7: Authentication Testing (4-8 uur)**
```
✓ Test auth bypass
✓ Test password reset
✓ Test session management
✓ Document & report
```

---

## 🛠️ HANDY BURP SUITE TIPS

### **Shortcuts:**
```
Ctrl+R     = Send to Repeater
Ctrl+I     = Send to Intruder
Ctrl+Shift+B = Base64 encode
Ctrl+Shift+U = URL encode
```

### **Repeater Tips:**
```
1. Right panel: Zie response
2. "Render" tab: Zie HTML rendered
3. "Hex" tab: Zie raw bytes
4. "Params" tab: Zie parameters overzichtelijk
5. "History" dropdown: Zie previous requests
```

### **Useful Features:**
```
1. Compare responses:
   - Right-click request > "Send to Comparer"
   - Repeat for second request
   - Tab "Comparer" > Click "Compare"

2. Match & Replace:
   - Automatically add X-HackerOne header
   - Proxy > Proxy settings > Match and Replace
   - Add rule:
     Type: Request header
     Match: ^$
     Replace: X-HackerOne: your_username

3. Filter HTTP History:
   - Filter bar: Show only "fanduel.com"
   - Hide: OPTIONS, static files (.js, .css, .png)
```

---

## ⚠️ COMMON MISTAKES (VERMIJD DEZE!)

### **1. Te agressief testen**
```
❌ FOUT: Run Nuclei/Gobuster
✅ GOED: Manual testing met Burp Repeater
```

### **2. Vergeten X-HackerOne header**
```
❌ FOUT: Requests zonder header
✅ GOED: Altijd X-HackerOne: username toevoegen
```

### **3. PII niet stoppen**
```
❌ FOUT: Hele database dumpen
✅ GOED: Stop bij eerste bewijs, report meteen
```

### **4. Slechte reports**
```
❌ FOUT: "There is XSS in profile"
✅ GOED: Complete report met steps, impact, screenshots
```

### **5. Out-of-scope bugs rapporteren**
```
❌ FOUT: "Missing security headers"
✅ GOED: Lees policy, test alleen in-scope bugs
```

### **6. Testing in production met echte users**
```
❌ FOUT: Test met echte user accounts
✅ GOED: Gebruik alleen je eigen test accounts
```

---

## 📈 SUCCESS METRICS

### **Week 1:**
```
✓ Tools geïnstalleerd en configured
✓ 2 test accounts aangemaakt
✓ Site volledig verkend
✓ 10+ endpoints gedocumenteerd
✓ Eerste IDOR tests gedaan
```

### **Week 2:**
```
✓ Alle user-facing endpoints getest
✓ XSS payloads uitgeprobeerd
✓ Authentication flows getest
✓ Misschien eerste bug gevonden!
```

### **Maand 1:**
```
✓ 1-3 valid bugs gerapporteerd
✓ Feedback van HackerOne ontvangen
✓ Begrip van hun response proces
✓ Routine ontwikkeld voor testing
```

---

## 🎓 LEARNING RESOURCES

### **Als je vast zit:**
```
1. HackerOne Hacktivity:
   https://hackerone.com/hacktivity
   - Zie disclosed reports
   - Leer van anderen

2. Hacker101:
   https://www.hacker101.com/
   - Gratis video's
   - CTF challenges

3. PortSwigger Academy:
   https://portswigger.net/web-security
   - Beste web security training
   - Gratis labs

4. OWASP Testing Guide:
   https://owasp.org/www-project-web-security-testing-guide/
```

---

## ✅ FINAL CHECKLIST VOOR JE START
```
□ HackerOne account aangemaakt
□ FanDuel account aangemaakt (met @wearehackerone.com email)
□ Tweede FanDuel test account aangemaakt
□ Burp Suite geïnstalleerd
□ Burp proxy geconfigureerd in browser
□ Burp CA certificate geïnstalleerd
□ X-HackerOne header genoteerd
□ FanDuel policy gelezen
□ Notitiebestand/tool gereed voor documentatie
□ Screenshots tool gereed (Windows: Snipping Tool, Mac: Cmd+Shift+4)
□ Deze guide opgeslagen voor referentie
```

---

## 🚀 START HIER - JE EERSTE UUR
```
MINUUT 0-10: Login & Explore
- Open browser met F12
- Login to FanDuel
- Network tab aan
- Browse 5 minuten

MINUUT 10-20: Document Endpoints
- Noteer alle /api/* calls
- Noteer URLs met IDs
- Screenshot interessante requests

MINUUT 20-40: First IDOR Test
- Pick een /api/user/{id} endpoint
- Open Burp > Proxy > HTTP History
- Find the request
- Send to Repeater
- Change ID
- Send
- Check response

MINUUT 40-60: Test More Endpoints
- Repeat IDOR test voor 5+ endpoints
- Document resultaten
- Als je bug vindt: Stop en document alles!
```

---

## 💬 LAATSTE TIPS

1. **Wees geduldig** - Eerste bug kan 20-40 uur duren
2. **Document alles** - Ook failed tests
3. **Quality over quantity** - 1 goede report > 10 slechte
4. **Leer van rejects** - Als je report wordt afgewezen, vraag waarom
5. **Stay ethical** - Volg altijd de rules
6. **Have fun!** - Dit is een puzzle, geniet ervan

---

## 🎯 WANNEER JE EEN BUG VINDT:
```
1. ✅ STOP testen van deze bug
2. ✅ TEST het 3x om te verifiëren
3. ✅ SCREENSHOT alles
4. ✅ DOCUMENT alle steps
5. ✅ CHECK impact
6. ✅ WRITE report (gebruik template)
7. ✅ SUBMIT op HackerOne
8. ✅ WAIT for response
9. ✅ RESPOND to questions within 24-48 uur
10. ✅ CELEBRATE! 🎉
````

