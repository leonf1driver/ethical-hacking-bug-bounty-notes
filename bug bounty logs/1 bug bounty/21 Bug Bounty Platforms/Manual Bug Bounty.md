- [ ] # 🎯 Bug Bounty Master Checklist

**Doel:** compact, praktisch en **Obsidian-vriendelijk** overzicht met de complete workflow, commands, stealth-instellingen, reporting-template en nuttige resources. Gebruik dit document tijdens elk bounty traject. **Test alleen binnen scope en met toestemming.**

---

## Inhoudsopgave

- High-level flow
    
- 1. Scope & rules
        
- 2. Passieve recon (OSINT)
        
- 3. Subdomain & asset enumeration
        
- 4. Light active discovery
        
- 5. Content discovery & parameter harvesting
        
- 6. Vulnerability probing
        
- 7. Exploitatie & bewijs
        
- 8. Rapportage-template
        
- 9. Follow-up & retest
        
- Tools per fase
    
- Low-noise command-sets (paste-ready)
    
- Stealth & etiquette
    
- OWASP / prioriteitenlijst
    
- Loggen / bewijs / Obsidian tips
    
- Handige resources & links
    

---

## 📈 High-level flow

1. Scope & rules check
    
2. Passieve recon (OSINT)
    
3. Subdomain / asset enumeratie
    
4. Web- & service discovery (lichte actieve checks)
    
5. Content / parameter discovery (zielige probes, geen brute force)
    
6. Vulnerability probing (handmatig + gecontroleerd tooling)
    
7. Proof-of-concept & bewijsverzameling (niet-destructief)
    
8. Rapportage & responsible disclosure
    
9. Follow-up / retest
    

---

## 1) Scope & rules (nooit overslaan)

- Lees programma pagina: **in-scope, out-of-scope, toegestane testen** (DoS? social engineering?), beloningen, contactgegevens.
    
- **Noteer:** domeinen, subdomeinen, IP-ranges, exclusion lists, disclosure timeline.
    
- Maak **screenshots** van scope/voorwaarden (bewijs).
    
- Documenteer toestemming en eventuele contactafspraken.
    

---

## 2) Passieve recon (niemand triggeren)

**Doel:** zoveel mogelijk informatie zonder interactie.

- `crt.sh`, `Censys`, `Shodan`, `Passive DNS`, `Common Crawl`, `Wayback Machine`.
    
- `GitHub` / `GitLab` search (API keys, secrets).
    
- Jobposts, LinkedIn, openbare documenten (PDF, XLS), paste sites.
    

|**Snelle Actie**|**Commando / Zoekopdracht**|
|---|---|
|**crt.sh**|`https://crt.sh/?q=%25.target.com`|
|**GitHub dork**|`site:github.com "target.com" "API_KEY"`|
|**Wayback/Crawl**|Gebruik `waybackurls` of `gau` later voor URL-harvest.|

---

## 3) Subdomain & asset enumeration

Combineer tools en dedupe:

Bash

```
subfinder -d target.com -o subfinder.txt
amass enum -passive -d target.com -o amass_passive.txt
cat subfinder.txt amass_passive.txt | sort -u > allsubs.txt
cat allsubs.txt | httpx -threads 15 -timeout 10 -status-code -title -o live_http.txt
```

> **Tip:** begin met threads 5–20. Verhoog alleen met toestemming.

---

## 4) Light active discovery (gericht & laag lawaai)

**Doel:** bepaal wat echt live is en welke services relevant zijn.

Bash

```
cat allsubs.txt | httpx -threads 20 -o httpx_out.txt
nmap -Pn -sV -p 80,443,8080,8443 --min-rate 20 --scan-delay 200ms -oA nmap_targeted 1.2.3.4
```

> Vermijd `-p-` of `-A` tenzij expliciet toegestaan.

---

## 5) Content discovery & parameter harvesting

### URL Harvesting

Bash

```
cat live_hosts.txt | while read host; do echo $host | waybackurls >> urls.txt; done
cat allsubs.txt | gau --subs >> gau_urls.txt
cat wayback_urls.txt gau_urls.txt | sort -u > all_urls.txt
```

### JS & Secrets

Bash

```
cat all_urls.txt | grep -E "\.js($|\?)" | sort -u > js_urls.txt
mkdir -p jsfiles
cat js_urls.txt | while read u; do sleep 0.2; wget -q -U 'Mozilla/5.0 (bugbounty; contact:you@me.com)' -P jsfiles "$u"; done
grep -RiE "api_key|apiToken|ACCESS_TOKEN|client_secret|key" jsfiles || true
```

### Directory brute-forcing (laag-noise)

Bash

```
ffuf -w /usr/share/wordlists/raft-small-directories.txt -u https://target.com/FUZZ -t 8 -p 0.2 -timeout 10 -o ffuf_out.json
gobuster dir -u https://target.com -w /usr/share/wordlists/dirb/common.txt -t 10 -s 200,301 -e -k
```

---

## 6) Vulnerability probing (handmatig + gecontroleerd tooling)

**Prioriteit:** authentication & authorization, data leakage, APIs, upload points. **Begin altijd non destructive.**

|**Kwetsbaarheid**|**Voorbeeld Testen**|
|---|---|
|**XSS**|Gebruik veilige PoC (console logs i.p.v. `alert()` indien nodig).|
|**IDOR**|Wijzig voorspelbare ID's en controleer toegang.|
|**SSRF**|Test met gecontroleerde externe endpoints (als toegestaan).|

> **Burp:** manueel testen, gebruik Intruder met throttle en low concurrency. Maak geen brute-force tenzij toegestaan.

---

## 7) Exploitatie & bewijs (niet-destructief)

- Bewijs moet **reproduceerbaar** maar **niet schadelijk** zijn.
    
- Gebruik screenshots, `curl` outputs, HTTP request/response fragments.
    
- **Redigeer gevoelige data (PII)** in rapporten.
    

Bash

```
# Voorbeeld capture
curl -i "https://target.com/search?q=%3Cscript%3Econsole.log('poc')%3C/script%3E" -H "User-Agent: MyPOCAgent" > poc_response.txt
```

---

## 8) Rapportage-template (kopieerbaar)

Gebruik dit template voor elke finding:

|**Sectie**|**Beschrijving**|
|---|---|
|**Titel:**|[bv. Reflected XSS in /search]|
|**Datum:**|YYYY-MM-DD|
|**Programma:**|`target.com`|
|**Scope:**|(exact uit programmapagina)|
|**Samenvatting:**|korte omschrijving + impact|
|**Severity:**|(CVSS of program scale)|
|**Finding:**|volledige technische beschrijving|
|**Reproductie-steps:**|stap-voor-stap (`curl` + minimale PoC)|
|**PoC (sanitized):**|minimal request/response/snippet|
|**Impact:**|wat kan een aanvaller doen?|
|**Mitigatie:**|concrete fix (voor devs)|
|**Bijlagen:**|logs, screenshots (geanonimiseerd), commands|

---

## 9) Follow-up & retest

- Wacht disclosure-periode volgens programma.
    
- Retest met dezelfde stappen en update rapport.
    
- Bewaar bewijs en tijdstempels.
    

---

## 🔨 Tools per fase (kort overzicht)

|**Fase**|**Tools**|
|---|---|
|**Passive**|`crt.sh`, `Censys`, `Shodan`, `PassiveDNS`, `waybackurls`, `gau`|
|**Enumeration**|`subfinder`, `amass`, `assetfinder`|
|**Probing**|`httpx`, `httprobe`, `whatweb`|
|**Scanning**|`nmap` (light), `nikto` (light), `nuclei` (templates)|
|**Discovery**|`ffuf`, `gobuster`, `feroxbuster`|
|**Exploitation**|Burp Suite, `sqlmap` (only if allowed), Metasploit (with care)|
|**Labs/Practice**|TryHackMe, HackTheBox, Intigriti labs|

---

## 💻 Low-noise command-sets (paste-ready)

### Subdomain harvest (passive-first):

Bash

```
subfinder -d target.com -o subfinder.txt
amass enum -passive -d target.com -o amass_passive.txt
cat subfinder.txt amass_passive.txt | sort -u > allsubs.txt
cat allsubs.txt | httpx -threads 15 -timeout 10 -status-code -title -o live_http.txt
```

### Gerichte nmap (laag-noise):

Bash

```
nmap -Pn -sV -p 80,443,8080,8443 --min-rate 20 --scan-delay 200ms -oN nmap_light target.com
```

### Lichte dirb/ffuf:

Bash

```
ffuf -w /usr/share/wordlists/raft-small-directories.txt -u https://target.com/FUZZ -t 8 -p 0.2 -timeout 10 -o ffuf_out.json
```

### JS harvesting & secrets grep:

Bash

```
cat all_urls.txt | grep -E "\.js($|\?)" | sort -u > js_urls.txt
mkdir -p jsfiles
cat js_urls.txt | while read u; do sleep 0.2; wget -q -U 'Mozilla/5.0 (bugbounty; contact:you@me.com)' -P jsfiles "$u"; done
grep -RiE "api_key|apiToken|ACCESS_TOKEN|client_secret|key" jsfiles || true
```

---

## 👻 Stealth & etiquette (belangrijk)

- **Threads:** 4–25; start laag.
    
- **Rate:** gebruik `--min-rate` / `--scan-delay` of `ffuf -p 0.2`.
    
- **Vermijd:** `-p-`, `-A`, `-p /--rate .-T5` tenzij toegestaan.
    
- Gebruik vertrouwde **User-Agent met contactgegevens** (soms verplicht).
    
- Trigger geen **DoS** of **data destruction**.
    
- Wees **transparant** als je per ongeluk iets raakt: meld aan het contact in scope.
    
- Bewaar **logs en timestamps** voor elk commando.
    

---

## 🎯 Prioriteitenlijst (wat eerst testen)

1. Authentication & authorization (IDOR, broken access control)
    
2. Sensitive info leakage (S3, API keys in JS)
    
3. SSRF / RCE / SQLi (hoog impact, only if allowed)
    
4. XSS (reflected/stored)
    
5. CSRF, clickjacking, insecure cookies
    
6. Misconfigurations (CORS, exposed dashboards)
    

---

## 📝 Logging & bewijs (praktisch)

- Sla altijd **raw requests/responses** op (geanonimiseerd waar nodig).
    
- Gebruik consistente **User-Agent** en noteer exact commando.
    
- **Screenshots:** Flameshot / native screenshot tools.
    
- **Obsidian:** Plak screenshots rechtstreeks met `Ctrl+V` of sleep file.
    
- **Captures:** Bewaar captures (`pcap`) als dat relevant is: `sudo tcpdump -i eth0 -w ~/Desktop/capture.pcap`.
    

---

## 💡 Obsidian tips

- Gebruik duidelijke **tags**: `#bugbounty #targetname #xss #idor`.
    
- **Versiebeheer:** houd `raw_commands.md` met exacte commando's en outputs (vergeet niet te anonimiseren voor rapport).
    

---

## 🌐 Handige resources

- TryHackMe, HackTheBox, Intigriti Labs
    
- PortSwigger Web Security Academy
    
- OWASP Top 10
    
- `crt.sh`, `waybackurls`, `gau`, `httpx`, `subfinder`, `amass`, `nuclei`