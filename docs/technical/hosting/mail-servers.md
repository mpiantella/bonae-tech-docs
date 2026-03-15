
Good setup to work with. Since your DNS is on Cloudflare but the domain is registered elsewhere (.com.ve via NIC.ve/101domain), you have the nameservers pointing to Cloudflare — which means you get full control of MX records and can use Cloudflare Email Routing for free. Here's the full layered architecture:

---

## The Right Architecture for Bonae Tech

```
bonaetech.com.ve
│
├── Cloudflare Email Routing (FREE)
│   ├── info@bonaetech.com.ve      → team Gmail
│   ├── soporte@bonaetech.com.ve   → team Gmail
│   └── hola@bonaetech.com.ve      → team Gmail
│
└── Zoho Mail — real mailboxes (PAID, ~$1/user/mo)
    ├── maria@bonaetech.com.ve      → real inbox, send & receive
    ├── jessica@bonaetech.com.ve    → real inbox
    └── veronica@bonaetech.com.ve   → real inbox

Client domains (e.g. cliente.com.ve)
└── Same pattern — Cloudflare forwarding or Zoho add-on domain
```

The reason for this split: Cloudflare Email Routing is **forwarding-only** — it can't send. So you use it for shared team aliases (`info@`, `soporte@`) that just need to land in someone's Gmail, and Zoho for real named mailboxes where clients reply directly to a person.

---

## Why Zoho Mail over Google Workspace

For a LATAM-focused startup, Zoho Mail wins on every practical dimension right now:

| | Zoho Mail | Google Workspace |
|---|---|---|
| **Price** | ~$1/user/mo (Mail Lite) | $6/user/mo |
| **Free tier** | ✅ Up to 5 users, 5GB each | ❌ |
| **Custom domain** | ✅ | ✅ |
| **Add-on domains** | ✅ Multiple (for client emails) | ✅ (but expensive at scale) |
| **Send from Gmail** | ✅ via SMTP | ✅ native |
| **LATAM support** | ✅ Spanish UI + support | ✅ |
| **IMAP/POP3** | ✅ | ✅ |

---

## Step-by-Step Setup

### Step 1 — Set up Cloudflare Email Routing (forwarding aliases)

In your Cloudflare dashboard → select your zone → **Email** → **Email Routing** → Enable.

Then add your forwarding rules:

```
info@bonaetech.com.ve      → maria@gmail.com (or team inbox)
soporte@bonaetech.com.ve   → jessica@gmail.com
hola@bonaetech.com.ve      → veronica@gmail.com
```

Cloudflare automatically adds the required MX and SPF records. Verify the destination Gmail addresses when prompted.

---

### Step 2 — Set up Zoho Mail for real mailboxes

Go to `zoho.com/mail` → **Sign Up for Free** → choose **Add my existing domain**.

Zoho will give you DNS records to add. In Cloudflare DNS, you'll need to:

**Add MX records** (Zoho's):
```
Type: MX  Name: @  Content: mx.zoho.com      Priority: 10
Type: MX  Name: @  Content: mx2.zoho.com     Priority: 20
Type: MX  Name: @  Content: mx3.zoho.com     Priority: 50
```

**Add SPF record** (TXT):
```
Type: TXT  Name: @
Content: v=spf1 include:zoho.com ~all
```

**Add DKIM record** (Zoho gives you this value):
```
Type: TXT  Name: zoho._domainkey
Content: v=DKIM1; k=rsa; p=<key Zoho provides>
```

**Add DMARC record** (recommended):
```
Type: TXT  Name: _dmarc
Content: v=DMARC1; p=none; rua=mailto:maria@bonaetech.com.ve
```

> ⚠️ **Important conflict to avoid:** Once you add Zoho's MX records, you must **disable** Cloudflare Email Routing or configure it to use a different subdomain — you can't have two MX setups on the same root domain. The clean split is: Zoho owns the MX (real mailboxes), and forwarding aliases are configured *inside* Zoho, not Cloudflare Email Routing.

---

### Step 3 — Configure aliases inside Zoho (team addresses)

Once Zoho is set up with your real mailboxes, create **email aliases** there instead of using Cloudflare routing:

In Zoho Admin Console → **Mail Administration** → **Email Aliases**:

```
info@bonaetech.com.ve     → delivers to maria + jessica + veronica
soporte@bonaetech.com.ve  → delivers to jessica
hola@bonaetech.com.ve     → delivers to maria
```

This way one email to `info@` lands in all three inboxes simultaneously.

---

### Step 4 — Send from Gmail using Zoho SMTP (optional but useful)

If the team prefers Gmail's interface but wants to send as `@bonaetech.com.ve`:

In Gmail → **Settings** → **Accounts** → **Add another email address**:
```
SMTP Server:  smtp.zoho.com
Port:         587  (TLS)
Username:     maria@bonaetech.com.ve
Password:     your Zoho app password
```

Now you read and send from Gmail but the From address shows `maria@bonaetech.com.ve` — zero additional cost.

---

### Step 5 — Client domain email (scaling the pattern)

For each client site Bonae Tech manages, two options:

**Option A — Zoho Add-on Domain** (if client wants real mailboxes)
Zoho Mail Lite allows multiple domains under one account. Add `cliente.com.ve` as an add-on domain, create their mailboxes, point their MX records in Cloudflare to Zoho. Bill the client for the mailbox.

**Option B — Cloudflare Email Routing only** (if client just needs `contacto@sudominio.com.ve` → their personal email)
Free, zero management overhead, done in 2 minutes per client via Cloudflare's dashboard or API — automatable via Terraform.

For automation at scale, Cloudflare's API lets you do this programmatically:
```bash
# Create an email routing rule via Cloudflare API
curl -X POST "https://api.cloudflare.com/client/v4/zones/{zone_id}/email/routing/rules" \
  -H "Authorization: Bearer $CF_TOKEN" \
  -H "Content-Type: application/json" \
  --data '{
    "matchers": [{"type": "literal", "field": "to", "value": "contacto@cliente.com.ve"}],
    "actions":  [{"type": "forward", "value": ["duenio@gmail.com"]}],
    "enabled": true,
    "name": "Cliente forwarding"
  }'
```

This can be wrapped in your Go CLI provisioning tool (US-13 from your backlog) so adding email for a new client is part of the same automated onboarding flow as domain + DNS + Pages deployment.

---

## Cost Reality Check First

For 3 users (María, Jessica, Verónica):

| | 101domain + Google Workspace | 101domain + Zoho Mail |
|---|---|---|
| **Domain (.com.ve/yr)** | ~$60–80 | ~$60–80 |
| **Email/mo** | $6/user × 3 = **$18/mo** | $1/user × 3 = **$3/mo** |
| **Email/year** | **$216/yr** | **$36/yr** |
| **Total year 1** | ~$276–296 | ~$96–116 |
| **Difference** | — | **$180 cheaper/yr** |

That $180/year gap matters in Venezuela. That said, Google Workspace has real advantages that might justify it — let's go through both.

---

## When Google Workspace IS Worth It

Google Workspace wins if your team already lives in Google's ecosystem:

- You use Google Docs/Sheets/Slides collaboratively
- You need Google Meet for client video calls
- You want Google Drive with 30GB+ pooled storage per user
- Clients expect `@bonaetech.com.ve` to show Google's professional sender reputation
- You plan to use Google's admin panel to manage a growing team

The **Business Starter plan at $6/user/mo** includes Gmail, Drive (30GB), Meet, Docs, Sheets, Slides, Calendar, and the full admin console. For a client-facing agency, that bundled value is real.

---

## Step-by-Step: 101domain + Google Workspace

### Step 1 — Register your domain on 101domain

Go to `101domain.com` → search `bonaetech.com.ve` → add to cart. During checkout you'll need:

```
- Business name: Bonae Tech
- RIF (Venezuelan tax ID) — required for .com.ve
- Physical address in Venezuela
- Contact email and phone
```

Registration typically takes 24–48 hours for `.com.ve` due to CONATEL's process. Expect ~$60–80/year.

---

### Step 2 — Point nameservers to Cloudflare (keep your stack intact)

Once the domain is active in 101domain, go to **DNS Management** in their dashboard and replace the default nameservers with Cloudflare's:

```
ns1.cloudflare.com  (exact values from your Cloudflare zone)
ns2.cloudflare.com
```

This keeps all DNS management in Cloudflare — you get the CDN, WAF, Pages integration, and full Terraform control. 101domain becomes pure registrar, nothing else.

---

### Step 3 — Sign up for Google Workspace

Go to `workspace.google.com` → **Get Started** → choose **Business Starter ($6/user/mo)**.

During setup:
```
Business name:  Bonae Tech
Number of users: 3
Domain: bonaetech.com.ve
→ Select "I already have a domain I want to use"
```

Google will ask you to verify domain ownership. The easiest method for Cloudflare DNS is the **TXT record method**:

Google gives you a value like:
```
google-site-verification=abc123xyz...
```

In Cloudflare DNS → Add record:
```
Type:    TXT
Name:    @
Content: google-site-verification=abc123xyz...
TTL:     Auto
```

Click verify in Google's setup wizard. Takes 1–5 minutes.

---

### Step 4 — Add MX records in Cloudflare

After verification, Google shows you 5 MX records to add. In Cloudflare DNS:

```
Type  Name  Mail server                 Priority
MX    @     ASPMX.L.GOOGLE.COM          1
MX    @     ALT1.ASPMX.L.GOOGLE.COM     5
MX    @     ALT2.ASPMX.L.GOOGLE.COM     5
MX    @     ALT3.ASPMX.L.GOOGLE.COM     10
MX    @     ALT4.ASPMX.L.GOOGLE.COM     10
```

---

### Step 5 — Add SPF, DKIM, and DMARC records

**SPF** (tells the internet Google is authorized to send for your domain):
```
Type:    TXT
Name:    @
Content: v=spf1 include:_spf.google.com ~all
```

**DKIM** (cryptographic signature — prevents spoofing):

In Google Admin Console → **Apps** → **Google Workspace** → **Gmail** → **Authenticate email** → Generate DKIM key → Copy the TXT record value.

In Cloudflare:
```
Type:    TXT
Name:    google._domainkey
Content: v=DKIM1; k=rsa; p=<long key Google generates>
```

**DMARC** (policy for what happens to spoofed emails):
```
Type:    TXT
Name:    _dmarc
Content: v=DMARC1; p=quarantine; rua=mailto:maria@bonaetech.com.ve
```

> Setting `p=quarantine` means spoofed emails go to spam. Once you're confident everything is working correctly, you can escalate to `p=reject`.

---

### Step 6 — Create user accounts

In Google Admin Console (`admin.google.com`):

```
Users → Add new user:

maria@bonaetech.com.ve     → María (admin)
jessica@bonaetech.com.ve   → Jessica
veronica@bonaetech.com.ve  → Verónica
```

---

### Step 7 — Create group aliases (team addresses)

In Google Admin Console → **Directory** → **Groups** → **Create group**:

```
info@bonaetech.com.ve      → all three members
soporte@bonaetech.com.ve   → jessica + maria
hola@bonaetech.com.ve      → maria
```

Groups are free and behave exactly like shared inboxes — email to `info@` lands in everyone's Gmail simultaneously.

---

### Step 8 — Client domain email at scale

For client domains managed by Bonae Tech, you have two clean options:

**Option A — Add client domain to Google Workspace**
Admin Console → **Domains** → **Add a domain** → add as *alias domain* or *secondary domain*. Then create `contacto@cliente.com.ve` as a user or group. This costs nothing extra per domain, only per additional user mailbox.

**Option B — Cloudflare Email Routing for simple forwarding**
For clients who just need `contacto@sudominio.com` to forward to their personal email, use Cloudflare Email Routing (free). This doesn't conflict with your Google Workspace setup since it's on a different domain's MX records.

---

## The Real Trade-off: 101domain + Google Workspace vs Namecheap/101domain + Zoho

```
Google Workspace advantages:
✅ Entire team works in one ecosystem (Docs, Drive, Meet, Calendar)
✅ Better sender reputation globally
✅ Superior mobile apps
✅ Easier for non-technical team members (Jessica, Verónica)
✅ Google Meet included — no Zoom needed for client calls
✅ Admin console is more polished for team management
✅ Better spam filtering

Zoho advantages:
✅ $180/year cheaper for 3 users
✅ More generous free tier (5 users free)
✅ Better for managing hundreds of client mailboxes at scale
✅ Add-on domain management cleaner for an agency model
⚠️ Inferior mobile experience
⚠️ Docs/Sheets not as good as Google's
```

---

## Honest Recommendation for Bonae Tech

Given that you're a team of three sisters building a client-facing agency, **Google Workspace is defensible** despite the cost, specifically because:

- Client video calls via Google Meet look more professional than a Zoho link
- The collaborative Docs/Sheets experience will matter as you build proposals, contracts, and reports together
- The admin console makes it trivial to add a fourth team member or contractor later
- The $18/month is a legitimate business expense — and at Venezuelan scale, likely recoverable from a single client's monthly retainer

The setup above keeps your architecture clean: **101domain as registrar → Cloudflare for DNS/CDN/Pages → Google Workspace for email and collaboration**. Each layer does one thing and does it well, with no vendor lock-in between them.