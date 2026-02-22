
GOOGLE DORKS: google hacking database zoek alleen de laatste de rest is gesloten

Zoek bv gmail passwords en in .txt

# Inlog & admin

Begin zoeken met site: 

site: tesla.com filetype:pdf

site:target.com

site:*.target.com

"@target.com" -site:target.com

inurl:admin site:target.com

inurl:login site:target.com

inurl:dashboard site:target.com

inurl:signup site:target.com

intitle:"login" site:target.com

intitle:"admin" site:target.com

“index off ” / “chat/logs”  site:

Filetype: sql  ext:sql

Filetype:sql “mysql dump”

Filetype:sql “mysql dump” (pass | password | pwd)

Inurl:index.php?id=

In de adresbalk     https://avrdc.org/?id=or:%271%27==%271%27__

# Open directories

intitle:"index of" site:target.com

intitle:"index of" "backup"

intitle:"index of" "config"

intitle:"index of" "uploads"

intitle:"index of /" parent directory site:target.com

# Documenten

filetype:pdf site:target.com

filetype:xls site:target.com

filetype:docx site:target.com

filetype:pptx site:target.com

filetype:txt site:target.com

# Config & gevoelige bestanden

ext:bak OR ext:old OR ext:backup site:target.com

ext:log site:target.com

ext:sql site:target.com

ext:env site:target.com

ext:ini site:target.com

ext:conf site:target.com

ext:xml site:target.com

ext:json site:target.com

ext:yml OR ext:yaml site:target.com

# Mogelijke secrets

"password" site:target.com

"secret" site:target.com

"api_key" site:target.com

"token" site:target.com

"confidential" site:target.com

"restricted" site:target.com

"not for distribution" site:target.com

"PRIVATE KEY" site:target.com

"BEGIN RSA PRIVATE KEY" site:target.com

"Authorization: Bearer" site:target.com

# Caching

cache:target.com

# Third-party leaks

site:pastebin.com "target.com"

site:github.com "target.com"

site:gitlab.com "target.com"

site:trello.com "target.com"

site:drive.google.com "target.com"

site:dropbox.com "target.com"

site:slack.com "target.com"

site:docs.google.com "target.com"

# Extra combinaties

site:target.com ext:env OR ext:yml OR ext:json "DB_PASSWORD"

site:target.com ext:log "Exception"

site:target.com ext:sql "INSERT INTO"

site:target.com "AWS_SECRET_ACCESS_KEY"

site:target.com "x-api-key"

site:target.com "OAuth"

site:target.com "Authorization: Basic"

# <mark class="hltr-b">ip adres zoeken</mark>