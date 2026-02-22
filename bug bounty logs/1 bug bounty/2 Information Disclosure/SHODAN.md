SHODAN commandos   (kijk naar de banners) andere meer op to date apps zijn sensys en zmap

ftp/anonymous/country:esp

ftp anonumous country:"NL"

webcamXP country:"NL"

ftp anonymous country:nl

**Belangrijkste Shodan‑operators**

- ip  
    Zoekt op exact IP-adres.  
    Voorbeeld: ip:8.8.8.8
- port  
    Filter op poortnummer.  
    Voorbeeld: port:22 (alle hosts met SSH open)
- hostname  
    Zoek in hostnaam/FQDN.  
    Voorbeeld: hostname:example.com  
    Gebruik wildcard: hostname:"*.example.com"
- net  
    Zoek binnen een netwerk/CIDR.  
    Voorbeeld: net:192.168.0.0/16
- org  
    Zoek op organisatie (WHOIS/hosting org).  
    Voorbeeld: org:"Amazon.com"
- isp  
    Filter op internetprovider.  
    Voorbeeld: isp:"Cloudflare, Inc."
- asn  
    Filter op Autonomous System Number.  
    Voorbeeld: asn:15169
- country  
    Landfilter (ISO‑landcode of naam).  
    Voorbeeld: country:"NL" of country:"Netherlands"
- city  
    Zoek op stad.  
    Voorbeeld: city:"Amsterdam"
- product  
    Filter op productnaam zoals in banners (webserver, device).  
    Voorbeeld: product:"Apache httpd" of product:"OpenSSH"
- version  
    Combineer met product om versie te targeten.  
    Voorbeeld: product:"nginx" version:"1.14.0"
- os  
    Filter op besturingssysteem.  
    Voorbeeld: os:"Windows 7"
- title  
    Zoek op HTTP-pagina titel (handig voor webapps).  
    Voorbeeld: title:"phpMyAdmin"
- http.favicon.hash  
    Zoek hosts met dezelfde favicon-hash (handig voor fingerprinting).  
    Voorbeeld: http.favicon.hash:123456789
- ssl  
    Filter op SSL/TLS aanwezig (true) of zoek in cert fields.  
    Voorbeeld: ssl:true of ssl.cert.subject.cn:"example.com"
- vuln (of vulns)  
    Zoek naar hosts met bekende kwetsbaarheden (CVE).  
    Voorbeeld: vuln:CVE-2017-5638
- has_screenshot  
    Hosts die Shodan-screenshots hebben (web GUI zichtbaar).  
    Voorbeeld: has_screenshot:true
- banner  
    Zoek vrije tekst in de service‑banner (raw).  
    Voorbeeld: banner:"Welcome to"
- before: / after:  
    Tijdsfilters op detectiedatum.  
    Voorbeeld: before:2024-01-01 of after:2025-01-01

---

**Combineer en verfijn**

- AND is impliciet: product:"Apache" port:80 country:"US"
- OR: title:"phpMyAdmin" OR title:"Admin"
- Exacte frasen met quotes: title:"Apache2 Ubuntu Default Page"

