
# 🔍 Information Gathering Report - Gifty.nl

**Datum:** 2026-01-11  

**Target:** dashboard.gifty.nl  

**Fase:** 1 - Reconnaissance & Analysis  

**Status:** ✅ Initial Analysis Complete

  

---

  

## 📋 Executive Summary

  

Eerste analyse van de Gifty.nl dashboard HTML source code. Belangrijke technische details, potentiële entry points en interessante endpoints geïdentificeerd.

  

---

  

## 🎯 Technology Stack

  

### Frontend Framework

- **Framework:** React/Vue (Single Page Application)

  - Bewijs: `<div id="gifty"></div>` → JavaScript rendered app

  - JavaScript files: `manifest.js`, `vendor.js`, `app.js`

### CDN & Hosting

- **CDN:** Cloudflare

  - Bewijs: `cdn.dashboard.gifty.nl`

  - Cloudflare Beacon: `static.cloudflareinsights.com`

  - CDN versie: `5734` (mogelijk build nummer)

  

### CSS Framework

- **Bootstrap:** Wordt gebruikt

  - File: `https://cdn.dashboard.gifty.nl/5734/css/bootstrap.css`

  

### Fonts

- **Open Sans** (Google Fonts via Cloudflare)

  - Meerdere weights: 300, 400, 600, 700

  - Unicode ranges: Latijn, Cyrillisch, Grieks, Hebreeuws, Vietnamees

  

### Monitoring & Tracking

- **Sentry:** Error tracking geïmplementeerd

  - Trace ID gevonden: `25f005906cbe4dd8bef990bfef9cba8f-a0fe6fe3a3b94b5a-1`

  - ⚠️ **Security Note:** Sentry traces kunnen soms gevoelige info bevatten

  

---

  

## 🔐 Security Tokens & Headers

  

### CSRF Token

```

Token: t9fObsPGxqFbnTvpmwl7dYChZJGzqqVIa7gvykTW

Location: <meta name="csrf-token" content="...">

```

  

**Bevindingen:**

- ✅ CSRF protection is geïmplementeerd

- ⚠️ Token is zichtbaar in HTML source (normaal voor meta tags)

- 🧪 **Test:** Kan dit token hergebruikt worden in andere sessies?

- 🧪 **Test:** Blijft token geldig na logout?

  

### Cloudflare Beacon Token

```

Token: d0e7a19eee914f9b96f007fa091d7421

Purpose: Performance monitoring & analytics

```

  

---

  

## 🗺️ Discovered Endpoints & Resources

  

### CDN Resources (Build 5734)

```

https://cdn.dashboard.gifty.nl/5734/css/bootstrap.css

https://cdn.dashboard.gifty.nl/5734/css/app.css

https://cdn.dashboard.gifty.nl/5734/js/manifest.js

https://cdn.dashboard.gifty.nl/5734/js/vendor.js

https://cdn.dashboard.gifty.nl/5734/js/app.js

https://cdn.dashboard.gifty.nl/5734/icons/ [multiple files]

```

  

**Interessante Observaties:**

- Build nummer `5734` in alle URLs

- 🧪 **Test:** Kunnen we oudere builds bereiken? (5733, 5732, 5000, etc.)

- 🧪 **Test:** Directory listing op `/5734/` of `/icons/`?

- 🧪 **Test:** Zijn er debug/source maps? (`app.js.map`, `vendor.js.map`)

  

### Icon Files

```

/icons/apple-icon-[57|60|72|76|114|120|144|152|180]x[57|60|72|76|114|120|144|152|180].png

/icons/android-icon-192x192.png

/icons/favicon-[16|32|96]x[16|32|96].png

/icons/manifest.json

```

  

**Test Prioriteit:**

- 🔴 **HIGH:** `/icons/manifest.json` - Kan app configuratie bevatten

- 🟡 **MEDIUM:** Directory listing op `/icons/`

  

---

  

## 🚨 Potential Entry Points

  

### 1. JavaScript Files Analysis

**Waarom belangrijk:**

- SPA's (Single Page Apps) bevatten vaak ALL routes in de bundle

- API endpoints kunnen hardcoded zijn

- Developer comments kunnen gevoelige info bevatten

  

**Acties:**

- [ ] Download en deobfuscate `app.js`

- [ ] Zoek naar API endpoints (regex: `/api/[a-z]+`)

- [ ] Zoek naar hardcoded secrets/tokens

- [ ] Zoek naar debug/development code

- [ ] Zoek naar commented out code met TODO's

  

**Tools:**

```bash

# Download JavaScript

wget https://cdn.dashboard.gifty.nl/5734/js/app.js

wget https://cdn.dashboard.gifty.nl/5734/js/vendor.js

wget https://cdn.dashboard.gifty.nl/5734/js/manifest.js

  

# Beautify voor analyse

js-beautify app.js > app_beautified.js

  

# Zoek naar interessante patterns

grep -i "api" app_beautified.js

grep -i "token" app_beautified.js

grep -i "secret" app_beautified.js

grep -i "password" app_beautified.js

grep -i "/admin" app_beautified.js

```

  

### 2. Manifest.json Analysis

**URL:** `https://cdn.dashboard.gifty.nl/5734/icons/manifest.json`

  

**Wat te verwachten:**

- App naam en beschrijving

- Start URL

- Mogelijk shortcuts naar admin panels

- Theme kleuren en icons

  

**Test:**

- [ ] Bezoek manifest.json

- [ ] Check voor onverwachte URLs in `start_url` of `shortcuts`

  

### 3. Cloudflare Endpoints

**Gevonden:**

```

/cdn-cgi/rum

/cdn-cgi/trace (standaard Cloudflare endpoint)

```

  

**Tests:**

- [ ] `https://dashboard.gifty.nl/cdn-cgi/trace` - IP info leak?

- [ ] Check Cloudflare security headers

- [ ] Test rate limiting

  

### 4. CSRF Token Reuse

**Current Token:** `t9fObsPGxqFbnTvpmwl7dYChZJGzqqVIa7gvykTW`

  

**Tests:**

- [ ] Gebruik token in andere browser/incognito

- [ ] Gebruik token na logout

- [ ] Check token expiratie tijd

- [ ] Test token in verschillende HTTP methods (GET vs POST)

  

---

  

## 🔍 Reconnaissance Next Steps

  

### Immediate Actions (Do First)

  

1. **JavaScript Analysis (HIGH PRIORITY)**

   ```bash

   # In Burp Suite of met curl

   curl https://cdn.dashboard.gifty.nl/5734/js/app.js -o app.js

   # Zoek naar routes

   cat app.js | grep -oP '"/[a-zA-Z0-9/_-]+"' | sort -u

   # Zoek naar API calls

   cat app.js | grep -oP 'api\.[a-zA-Z0-9._]+' | sort -u

   ```

  

2. **Robots.txt Check**

   ```

   URL: https://dashboard.gifty.nl/robots.txt

   ```

   - [ ] Bezoek en documenteer Disallowed paths

   - [ ] Voeg gevonden paths toe aan Burp Target

  

3. **Manifest.json Check**

   ```

   URL: https://cdn.dashboard.gifty.nl/5734/icons/manifest.json

   ```

   - [ ] Analyseer app configuratie

   - [ ] Check voor shortcuts/start_url

  

4. **Source Maps Check**

   ```

   URLs to test:

   https://cdn.dashboard.gifty.nl/5734/js/app.js.map

   https://cdn.dashboard.gifty.nl/5734/js/vendor.js.map

   https://cdn.dashboard.gifty.nl/5734/css/app.css.map

   ```

   - ⚠️ **CRITICAL:** Source maps bevatten originele source code!

   - Als gevonden: Download en analyseer voor credentials/logic

  

5. **Version/Build Enumeration**

   ```

   Test builds:

   /5735/ (newer)

   /5733/ (older)

   /5700/, /5600/, /5500/ (much older)

   /dev/, /test/, /staging/ (development builds)

   ```

  

---

  

## 🎯 Potential Vulnerabilities to Test

  

### High Priority

  

1. **Information Disclosure via JavaScript**

   - Hardcoded API keys

   - Admin endpoints in route definitions

   - Debug/development code

   - Source maps exposure

  

2. **CSRF Token Issues**

   - Token reuse across sessions

   - Token not validated server-side

   - Token predictability

  

3. **CDN Misconfiguration**

   - Directory listing enabled

   - Old builds accessible (may have known vulnerabilities)

   - Staging/dev builds publicly accessible

  

### Medium Priority

  

1. **Sentry Information Leak**

   - Check if Sentry DSN is exposed in JavaScript

   - Old error reports might contain sensitive data

  

2. **Cloudflare Bypass**

   - Find origin IP via `/cdn-cgi/trace`

   - Test if origin server directly accessible

  

---

  

## 📊 Metadata Findings

  

### Browser Compatibility

```html

<meta name="viewport" content="width=device-width, user-scalable=no,

      initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">

```

- Responsive design

- User scaling disabled (accessibility issue, maar geen security bug)

  

### Theme & Branding

```html

<meta name="theme-color" content="#ffffff">

<title>Gifty</title>

<meta name="description" content=""/> <!-- Empty! -->

```

- ⚠️ Empty meta description (SEO issue, geen security impact)

  

---

  

## 🧪 Testing Checklist

  

### Phase 1: Static Analysis (Zonder interactie)

- [ ] Download en analyseer app.js

- [ ] Download en analyseer vendor.js

- [ ] Check voor source maps (.map files)

- [ ] Analyseer manifest.json

- [ ] Check robots.txt

- [ ] Enumerate build numbers (5734 → 5733, 5735, etc.)

  

### Phase 2: Dynamic Analysis (Met Burp)

- [ ] Intercept requests en check CSRF token usage

- [ ] Test CSRF token in Burp Repeater

- [ ] Check response headers voor security misconfiguration

- [ ] Test directory listing op CDN paths

- [ ] Map alle API endpoints die app.js aanroept

  

### Phase 3: IDOR Preparation

- [ ] Lijst alle endpoints met ID parameters uit JavaScript

- [ ] Identificeer predictable vs unpredictable IDs

- [ ] Prepare Burp Intruder payloads

  

---

  

## 🔗 URLs for Further Investigation

  

```

✅ Confirmed Domains:

- dashboard.gifty.nl (main app)

- cdn.dashboard.gifty.nl (static assets)

- api.gifty.nl (API - assumed, needs verification)

  

🔍 To Discover:

- Auth endpoints (/auth/login, /auth/callback?)

- API endpoints (/api/v1/...?)

- Admin panels (/admin?, /backend?)

- Development environments (dev.gifty.nl?, staging.gifty.nl?)

```

  

---

  

## 💡 Key Insights

  

1. **Modern Stack:** React/Vue SPA betekent dat alle routes in JavaScript zitten

2. **Cloudflare Protected:** Direct origin access waarschijnlijk niet mogelijk

3. **CSRF Protected:** Meta token aanwezig, maar moet getest worden

4. **CDN Versioning:** Build 5734 - mogelijkheid om oude builds te vinden

5. **Sentry Enabled:** Error tracking = mogelijk information disclosure

  

---

  

## ⚠️ Red Flags Found

  

1. ❌ **Sentry Trace ID visible** - Kan gebruikt worden voor fingerprinting

2. ⚠️ **Build number exposed** - Maakt version enumeration mogelijk

3. ⚠️ **Empty meta description** - Minor issue

4. ✅ **CSRF token present** - Goede security practice

  

---

  

## 📝 Next Steps Priority

  

**🔴 CRITICAL - Do First:**

1. Download en analyseer `app.js` voor API endpoints en routes

2. Check `manifest.json` voor app configuratie

3. Test voor source maps (`.map` files)

  

**🟡 HIGH - Do Soon:**

4. Enumerate build numbers (5733, 5732, etc.)

5. Check `robots.txt` voor hidden paths

6. Test CSRF token behavior in Burp Repeater

  

**🟢 MEDIUM - Do Later:**

7. Analyze all icon files (unlikely to find issues)

8. Test Cloudflare `/cdn-cgi/` endpoints

9. Map complete JavaScript route structure

  

---

  

## 🎓 Learning Notes

  

**Wat heb ik geleerd uit deze HTML source:**

- Modern web apps verschuilen veel functionaliteit in JavaScript

- Meta tags kunnen security tokens bevatten (CSRF)

- CDN versioning kan leiden tot information disclosure

- Sentry traces kunnen gebruikt worden voor fingerprinting

- SPA's vereisen JavaScript reverse engineering voor volledige mapping

  

**Volgende fase in cursus:**

- JavaScript Analysis & Endpoint Discovery

- API Endpoint Mapping

- IDOR Testing op gevonden endpoints

  

---

  

**Status:** Ready for Phase 2 - JavaScript Analysis  

**Risk Level:** Information Gathering = Low Risk (Passive)  

**Compliance:** ✅ Binnen scope, geen active exploitation