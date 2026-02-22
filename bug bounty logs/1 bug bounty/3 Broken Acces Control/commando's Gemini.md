
Hier is de complete invulling voor je **Broken Access Control (BAC)** notitie. Ik heb het opgesplitst in IDOR, Privilege Escalation en 403 Bypasses.

Kopieer en plak dit direct in je Obsidian file:

Markdown

````
# 🚫 Broken Access Control (BAC)

==Dit is de #1 vulnerability in de OWASP Top 10. Scanners missen dit vaak!==

---

## 🆔 1. IDOR (Insecure Direct Object Reference)
*Het toegang krijgen tot data van anderen door simpelweg een ID aan te passen.*

**Handmatige Check:**
Zoek naar API calls of URL's met identifiers:
* `/api/users/1001` -> Verander naar `1002`
* `/invoice?id=500` -> Verander naar `501`
* `{"user_id": 123}` -> Verander naar `124`

**Tools:**
Voor IDOR is **Burp Suite** met de **Autorize** plugin de gouden standaard.
Maar je kunt endpoints ook fuzz-testen met FFUF als de ID's opeenvolgend (1, 2, 3) zijn.

**FFUF ID Fuzzing Command:**
*Vervang FUZZ met nummers om te kijken of je andere accounts ziet.*
```bash
ffuf -u [https://target.com/api/profile/FUZZ](https://target.com/api/profile/FUZZ) -w numbers_1-1000.txt -mc 200 -fs 1500
````

_(Uitleg: `-mc 200` zoekt naar successen, `-fs 1500` filtert responses die dezelfde grootte hebben als errors)._

---

## 🪜 2. Privilege Escalation

_Proberen acties uit te voeren die alleen admins mogen._

**Vertical Escalation (User -> Admin):**

1. Maak twee accounts: User A (Low) en Admin B (High).
    
2. Pak het cookie/token van User A.
    
3. Probeer admin-endpoints te benaderen met het token van User A.
    

**Admin Panel Discovery (FFUF):** Zoek naar verborgen admin portalen die niet goed beveiligd zijn.

Bash

```
ffuf -u [https://target.com/FUZZ](https://target.com/FUZZ) -w /path/to/seclists/Discovery/Web-Content/raft-medium-directories.txt -mr "admin|login|panel"
```

---

## 🚧 3. 403 Bypass (Forbidden Access)

_De server zegt "Nee (403)", maar misschien kun je er toch langs._

Als je een `/admin` pagina vindt die "403 Forbidden" geeft, probeer dan deze technieken.

**Automatische Tool (403bypasser / Nomore403):** Er zijn tools die alle trucs automatisch proberen.

Bash

```
# Voorbeeld met nomore403 (Go tool)
nomore403 -u [https://target.com/admin](https://target.com/admin)
```

**Handmatige Curl Trucs (Headers):** Servers vertrouwen soms specifieke headers. Test deze één voor één:

Bash

```
# 1. X-Custom-IP-Authorization (Doe alsof je localhost bent)
curl -H "X-Forwarded-For: 127.0.0.1" [https://target.com/admin](https://target.com/admin)
curl -H "X-Originating-IP: 127.0.0.1" [https://target.com/admin](https://target.com/admin)

# 2. URL Manipulatie (Puntjes en slashes)
curl [https://target.com/admin/](https://target.com/admin/)
curl [https://target.com/admin](https://target.com/admin).
curl [https://target.com//admin//](https://target.com//admin//)
curl [https://target.com/./admin/./](https://target.com/./admin/./)

# 3. Header Overwrite
curl -H "X-Original-URL: /admin" [https://target.com/anything](https://target.com/anything)
curl -H "X-Rewrite-URL: /admin" [https://target.com/anything](https://target.com/anything)
```

---

## 🔄 4. HTTP Verb Tampering

_Soms is `GET /admin/deleteUser` geblokkeerd, maar `POST` of `HEAD` niet._

Probeer de actie uit te voeren met een ander HTTP werkwoord.

**Curl Commando:**

Bash

```
# Test verschillende methods op hetzelfde endpoint
curl -X POST [https://target.com/admin/users](https://target.com/admin/users)
curl -X PUT [https://target.com/admin/users](https://target.com/admin/users)
curl -X HEAD [https://target.com/admin/users](https://target.com/admin/users) -I
```

---

## 📝 Checklist voor BAC

- [ ] **Multi-Account:** Heb ik 2 users aangemaakt om data te kruisen?
    
- [ ] **UUIDs:** Gebruiken ze lange codes (UUID) of simpele nummers (1, 2, 3)?
    
- [ ] **Referer Header:** Checkt de site waar ik vandaan kom? (Probeer header te verwijderen).
    
- [ ] **Mobiele API:** Hebben ze een mobiele app? Die API's zijn vaak minder goed beveiligd dan de website.
    

```

### Extra tip voor je Obsidian setup
Bij Broken Access Control is het **bewijs (Proof of Concept)** cruciaal.

Als je een IDOR vindt, maak dan altijd screenshots van:
1.  Het scherm van **Slachtoffer** (met zijn privégegevens).
2.  Het scherm van **Aanvaller** (die de data van het slachtoffer ziet).

Dit verschil maakt je rapport "Critical".
```
