
### Reconnaissance (Subdomeinen & Assets)

Dit is de belangrijkste fase. Hoe meer subdomeinen je vindt, hoe groter je aanvalsoppervlak.

**Subfinder** (Snel passief subdomeinen vinden)

- **Doel:** Verzamelt subdomeinen van publieke bronnen.
    
- **Commando:**
    
    Bash
    
    ```
    subfinder -d target.com -o subdomains.txt
    ```
    

**Amass** (Diepe enumeratie)

- **Doel:** Veel uitgebreider (en trager) dan Subfinder; gebruikt scraping, certificaten en API's.
    
- **Commando:**
    
    Bash
    
    ```
    amass enum -passive -d target.com -src
    ```
    

**Httpx** (Live check)

- **Doel:** Kijken welke gevonden subdomeinen daadwerkelijk 'live' zijn (HTTP/HTTPS server draaien).
    
- **Commando:**
    
    Bash
    
    ```
    cat subdomains.txt | httpx -title -tech-detect -status-code -o live_subs.txt
    ```
    

---

### 2. Directory & File Fuzzing

Nadat je de domeinen hebt, wil je verborgen bestanden en mappen vinden (zoals `.git`, `/admin`, `backup.sql`).

**FFUF** (Fuzzy Finder - De snelste)

- **Doel:** Razendsnelle web fuzzer.
    
- **Vereiste:** Je hebt een wordlist nodig (bijv. Seclists).
    
- **Commando:**
    
    Bash
    
    ```
    ffuf -u https://target.com/FUZZ -w /path/to/wordlist.txt -mc 200,301
    ```
    

**Gobuster** (Het klassieke alternatief)

- **Doel:** Zelfde als FFUF, vaak makkelijker in gebruik voor beginners.
    
- **Commando:**
    
    Bash
    
    ```
    gobuster dir -u https://target.com -w /path/to/wordlist.txt
    ```
    

---

### 3. Content Discovery & Links

Oude URL's bevatten vaak kwetsbaarheden die in de hoofdapplicatie al gepatcht zijn.

**Waymore / Gao** (Wayback Machine scraper)

- **Doel:** Haalt historische URL's op uit het internetarchief.
    
- **Commando:**
    
    Bash
    
    ```
    waymore -i target.com -mode U -o urls.txt
    ```
    

**Katana** (Next-gen Crawler)

- **Doel:** Crawlt de website dynamisch om alle links en endpoints te vinden.
    
- **Commando:**
    
    Bash
    
    ```
    katana -u https://target.com -d 5 -o crawled_urls.txt
    ```
    

---

### 4. Vulnerability Scanning (Automated)

Gebruik deze tools om snel laaghangend fruit te vinden.

**Nuclei** (De absolute nummer 1 scanner)

- **Doel:** Template-based scanning. Het checkt op duizenden bekende CVE's en misconfiguraties.
    
- **Commando:**
    
    Bash
    
    ```
    nuclei -l live_subs.txt -t nuclei-templates/ -o vulnerabilities.txt
    ```
    

**Burp Suite** (De Manual King)

- **Doel:** Proxy tool om verkeer te onderscheppen en aan te passen.
    
- **Tip:** De _Community Edition_ is gratis, maar voor automation heb je de _Professional_ versie nodig.
    

---

### 5. Specifieke Bug Classes (Exploitation)

Tools gericht op één specifiek type lek.

**Dalfox** (XSS Scanner)

- **Doel:** Checkt parameters op Cross-Site Scripting.
    
- **Commando:**
    
    Bash
    
    ```
    cat urls_with_params.txt | dalfox pipe
    ```
    

**SQLmap** (SQL Injection)

- **Doel:** Automatische detectie en exploitatie van SQL injections.
    
- **Commando:**
    
    > [!INFO] Bash
    
    ```
    sqlmap -u "http://target.com/page.php?id=5" --dbs --batch
    ```
    

**Arjun** (Hidden Parameter Discovery)

- **Doel:** Vindt parameters die niet zichtbaar zijn in de HTML (onzichtbare inputs).
    
- **Commando:**
    
    Bash
    
    ```
    arjun -u https://target.com/endpoint
    ```
    

---

### 6. Helper Tools & 'Glue'

Tools die je output filteren en leesbaar maken.

- **GF (Grep Fuzz):** Om patronen te filteren uit grote tekstbestanden (bijv. `cat urls.txt | gf xss`).
    
- **Anew:** Voegt regels toe aan een bestand, maar alleen als ze nog niet bestaan (voorkomt duplicaten).
    
- **Notify:** Stuurt resultaten direct naar je Discord of Slack.
    

---

### De "Golden Pipeline" (Voorbeeld)

Veel hunters "chainen" deze tools aan elkaar in één bash script. Hier is een voorbeeld van een mini-workflow:

Bash

```
# 1. Vind subdomeinen
subfinder -d target.com -silent | anew subs.txt

# 2. Check welke live zijn
cat subs.txt | httpx -silent | anew live.txt

# 3. Crawl de live sites voor URL's
cat live.txt | katana -silent | anew urls.txt

# 4. Scan alles met Nuclei
nuclei -l live.txt -severity low,medium,high,critical
```

> **Belangrijke notitie:** Zorg altijd dat je de **Wordlists** van Daniel Miessler (SecLists) hebt geïnstalleerd. Zonder goede wordlists zijn tools als FFUF en Gobuster nutteloos.

