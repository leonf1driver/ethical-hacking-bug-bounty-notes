
Omdat je specifiek om **IDOR** vroeg (en dit mijn persoonlijke favoriet is omdat het makkelijk te vinden is én goed betaalt):

Hier is de **Master Note** voor **IDOR**.

---

Markdown

````
# 🆔 IDOR (Insecure Direct Object Reference)

==Concept: De applicatie controleert niet of de gebruiker die de data opvraagt, ook daadwerkelijk de eigenaar is van die data.==
*Voorbeeld: Ik verander `id=100` naar `id=101` en zie plotseling de factuur van mijn buurman.*



---

## 🕵️‍♂️ 1. Waar moet je zoeken? (Locate IDs)

Zoek naar elk verzoek waarbij een **identifier** wordt gebruikt om data op te halen of te wijzigen.

**De duidelijke plekken (URL Parameters):**
* `/api/users/12345`
* `/invoice?id=50`
* `/messages/view?msg_id=99`

**De verstopte plekken (Headers & Body):**
* **JSON Body:** `{"user_id": 100, "amount": 50}`
* **Cookies:** `Cookie: session=...; account_id=100;`
* **Custom Headers:** `X-User-ID: 100`

---

## 🧪 2. De Gouden Test Methode (P1 Hunting)
*IDOR testen doe je NOOIT met één account. Je hebt er twee nodig.*

**De Setup:**
1.  Maak **Account A** (Aanvaller).
2.  Maak **Account B** (Slachtoffer).
3.  Log in op Account B en zoek het ID van een privé object (bijv. Factuur ID: `555`).
4.  Log in op Account A.
5.  Vang een request van Account A in Burp Suite.
6.  **De Hack:** Verander het ID in het request van Account A naar `555` (van B).

**Resultaat:**
* **200 OK + Data van B:** 🚨 **IDOR (BOUNTY!)**
* **403 Forbidden:** Veilig (Access Control werkt).
* **404 Not Found:** Veilig (Of ID bestaat niet).

---

## 🛠️ 3. Bypassing IDOR Protection
*Soms is het niet zo simpel als een nummertje veranderen. Probeer deze trucs.*

### A. HTTP Parameter Pollution (HPP)
De server checkt de *eerste* parameter, maar gebruikt de *tweede*.
```http
GET /api/profile?user_id=AANVALLER_ID&user_id=SLACHTOFFER_ID
GET /api/profile?user_id=SLACHTOFFER_ID&user_id=AANVALLER_ID
````

_Soms werkt een array ook:_ `user_id[]=SLACHTOFFER_ID`

### B. JSON Parameter Pollution

JSON

```
{"user_id": 100, "user_id": 101}
```

### C. Type Juggling (Wrapper Bypass)

Als de server verwacht dat het ID een nummer is, stuur dan JSON of een array. Soms crasht de check, maar gaat de query door.

- `id=123` -> `id={"id":123}`
    
- `id=123` -> `id=[123]`
    

### D. Method Tampering

Als `GET /users/100` beveiligd is, probeer dan andere acties.

- `POST /users/100`
    
- `PUT /users/100`
    
- `DELETE /users/100`
    

### E. ID Guessing / Leaking (UUIDs)

Als de site UUIDs gebruikt (550e8400-e29b...) kun je die niet raden.

Maar... ze lekken vaak ergens anders!

1. Kijk in de broncode (Ctrl+U).
    
2. Kijk in API responses van "Public Profiles".
    
3. Kijk in foutmeldingen.
    
    Heb je de UUID gevonden? Plak hem in het IDOR request.
    

---

## 🤖 4. Automation (Burp Autorize)

_Handmatig IDOR testen is traag. Autorize doet dit volledig automatisch._

**Hoe werkt het?**

1. Installeer **Autorize** (gratis) uit de BApp Store.
    
2. Pak het cookie/token van **Account A** (Aanvaller) en zet dit in Autorize.
    
3. Zet Autorize "ON".
    
4. Klik nu rond in de browser als **Account B** (Slachtoffer/Admin).
    
5. **De Magic:** Autorize pakt elk request van B, vervangt de cookies met die van A, en kijkt of het lukt.
    
6. Kijk in de tab "Autorize" naar **Bypassed!** (Rood) of **Enforced** (Groen).
    

---

## 🧱 5. Impact Verhogen (Van P4 naar P1)

_Een IDOR is niet altijd veel geld waard. Het gaat om de impact._

|**Actie**|**Impact**|**Bounty**|
|---|---|---|
|**Add/Delete items** in winkelmandje ander|Low/Medium|$100 - $500|
|**Lezen prive gegevens** (Naam/Adres/Tel)|High|$1000 - $3000|
|**Account Takeover** (Email/Wachtwoord wijzigen)|**Critical**|$5000+|

> [!TIP] Zoek naar "Write" IDORs
> 
> Veel hunters checken alleen of ze iets kunnen lezen (GET).
> 
> Check altijd of je iets kunt wijzigen (POST/PUT) bij een ander. Wachtwoord resetten? Email aanpassen? Adres wijzigen?

---

## 📝 Checklist IDOR

- [ ] **Twee Accounts:** Heb ik getest met User A vs User B?
    
- [ ] **Autorize:** Heb ik de plugin mee laten draaien tijdens het browsen?
    
- [ ] **Andere Objecten:** Niet alleen Users! Check ook: `invoice_id`, `address_id`, `payment_id`, `photo_id`.
    
- [ ] **Blind IDOR:** Verander het ID bij een `DELETE` of `POST` actie. Krijg je geen error? Dan is het misschien gelukt (check met het andere account).
    
- [ ] **Unpredictable IDs:** Gebruiken ze hashes? Probeer ze te decoden (Base64/Hex).