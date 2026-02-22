
Dit is een fantastische categorie voor **High/Critical** bounties. OAuth-fouten leiden vaak direct tot **Account Takeover (ATO)**. Omdat het protocol ingewikkeld is, maken developers hier vaak fouten in de implementatie.

Hier is de complete **Obsidian Notitie** voor OAuth 2.0.

---

Markdown

````
# 🔑 OAuth 2.0 & OpenID Connect

==Concept: Een manier om in te loggen via een derde partij (bijv. "Login with Google/Facebook").==
*Het doel is vaak om de `authorization code` of het `access_token` te stelen, of om een account te koppelen aan dat van de aanvaller.*



---

## 🕵️‍♂️ 1. Recon: Herken de Flow

Zoek naar knoppen als **"Sign in with..."** of **"Link Account"**.
Kijk in de URLbalk of Burp History naar verzoeken met deze parameters:

* `client_id=` (De app die toegang vraagt)
* `redirect_uri=` (Waar de user heen gaat na login - **JOUW DOELWIT**)
* `response_type=` (code, token, id_token)
* `scope=` (email, profile, contacts)
* `state=` (CSRF bescherming)

---

## 🎯 2. Attack Vector: Redirect URI Manipulation
*Dit is de meest voorkomende bug. De server stuurt de code/token naar de URL in deze parameter. Als je die kunt veranderen naar jouw server, steel je het account.*

**Stap 1: Intercept het Auth Request**
Vang het verzoek in Burp Suite wanneer je op "Login" klikt:
```http
GET /authorize?response_type=code&client_id=...&redirect_uri=[https://target.com/callback](https://target.com/callback) HTTP/1.1
````

**Stap 2: Manipuleer de `redirect_uri`** Probeer deze variaties in Burp Repeater. Als de server redirect naar jou, heb je gewonnen.

> [!EXAMPLE] URI Bypasses **1. Open Redirect:** `redirect_uri=https://evil.com`
> 
> **2. Path Traversal / Open Redirect op target:** `redirect_uri=https://target.com/logout?next=https://evil.com`
> 
> **3. Weak Regex (Matcht begin):** `redirect_uri=https://target.com.evil.com`
> 
> **4. Parameter Pollution:** `redirect_uri=https://target.com/callback&redirect_uri=https://evil.com`
> 
> **5. IPv6 / Localhost (Dev foutjes):** `redirect_uri=http://localhost` `redirect_uri=http://[::1]`

---

## 🎭 3. Attack Vector: OAuth CSRF (State Parameter)

_Als de `state` parameter ontbreekt of niet gecheckt wordt, kun je jouw account aan dat van het slachtoffer koppelen._

**Scenario:**

1. De aanvaller logt in met _zijn_ Google account op de target site, maar stopt _vóór_ de laatste redirect.
    
2. Aanvaller kopieert de URL (met _zijn_ auth code).
    
3. Aanvaller stuurt link naar Slachtoffer.
    
4. Slachtoffer klikt -> Logt in -> Het Google account van de **aanvaller** is nu gekoppeld aan het account van het **slachtoffer**.
    
5. Aanvaller logt in met Google en zit in het account van het slachtoffer.
    

**Checklist:**

- [ ] Is de `state` parameter aanwezig?
    
- [ ] Als ik de `state` weghaal, werkt de login dan nog? (Zo ja -> **Lek!**)
    
- [ ] Is de `state` voorspelbaar (bijv. altijd "1234" of "state")?
    

---

## 🔓 4. Attack Vector: Implicit Flow (Response Type)

_Soms lekt het token direct in de URL (hash fragment) in plaats van via een veilige code backend-to-backend._

**De truc:** Verander `response_type=code` naar `response_type=token,id_token`.

HTTP

```
GET /authorize?response_type=token,id_token&...
```

- **Resultaat:** Als de server dit ondersteunt, krijg je het token direct terug in de browser URL (`#access_token=...`). Dit wordt vaak gelogd in browser history of proxies en is minder veilig. Rapporteer dit als de site gevoelige data bevat.
    

---

## 👤 5. Attack Vector: Pre-Account Takeover

_Een venijnige bug die vaak P2/P3 oplevert._

1. **Aanvaller:** Maakt een account aan op `target.com` met email `slachtoffer@gmail.com` (en een eigen wachtwoord). Verifieert de email _niet_.
    
2. **Slachtoffer:** Komt later op de site en denkt "Hé, ik log in met Google" (met datzelfde gmail adres).
    
3. **Target:** Ziet de email match, en logt de user in _zonder_ wachtwoord check (want Google wordt vertrouwd).
    
4. **Bug:** Het account is nu "geverifieerd" door Google, maar de aanvaller kent het wachtwoord nog van stap 1 en kan nu inloggen in het account van het slachtoffer.
    

---

## 🛠️ 6. Tools & Automation

OAuth is lastig volledig te automatiseren, maar deze extensies helpen in Burp.

**Burp Suite Extensions (BApp Store):**

- **OAuth Scan:** Scant automatisch op redirect URI bypasses.
    
- **EsPReSSO:** Helpt bij het visualiseren en manipuleren van JWT/OAuth tokens.
    

**Manuele Helper (Decoder):** OAuth tokens (JWT) zijn Base64 encoded.

1. Kopieer token (`eyJhbGci...`).
    
2. Ga naar **jwt.io** of Burp Inspector.
    
3. Kijk of er gekke data in zit (bijv. `"admin": false` -> verander naar `true` en zet Signature op "None").
    

---

## 📝 Checklist OAuth

- [ ] **Scope:** Kan ik `scope=email` veranderen naar `scope=admin` of `scope=contacts`?
    
- [ ] **State:** Heb ik gecheckt of de state parameter verplicht en uniek is?
    
- [ ] **Redirect:** Heb ik `evil.com` en `localhost` geprobeerd?
    
- [ ] **Code Replay:** Kan ik een gebruikte Authorization Code _nog een keer_ gebruiken? (Zou niet mogen).
    
- [ ] **AccessToken:** Verloopt het token als ik uitlog? (Soms blijven ze geldig -> Info Disclosure).
    

```

### Tip voor je workflow
OAuth bugs zijn complex om uit te leggen in een rapport.
**Tip:** Maak altijd een **video** of een **diagram** (zoals een sequence diagram) voor je report op HackerOne. Dat vergroot de kans dat de Triage het snapt en uitbetaalt.

