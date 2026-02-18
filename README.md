# C.H.A.N.C.E. For Crime Victims

**Connecting Help, Advocacy, Navigation, Counsel & Education**

Website for C.H.A.N.C.E., a North Carolina nonprofit dedicated to serving crime victims through legal advocacy, education, and navigation services. The organization's initial project focuses on military-connected survivors of sexual assault, domestic violence, and harassment.

---

## Project Structure

```
ncchance.org/
├── index.html                  # Main single-page website
├── images/
│   ├── ChanceLogo_2.svg        # Full organization logo
│   ├── ChanceBadge.svg         # Shield badge (favicon + hero)
│   └── ruth-cresenzo.jpg       # Executive Director headshot
├── docs/
│   └── IRS-501c3-Status-Granting-Letter-2023.pdf
├── .gitignore
└── README.md
```

## Tech Stack

- Pure HTML/CSS/JS — no build tools or frameworks
- Single-page layout with smooth scroll navigation
- Google Fonts: DM Sans
- Inline SVG icons (no external icon libraries)
- JSON-LD structured data (NGO schema)

## Development

Open `ncchance.html` directly in a browser, or use the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension in VS Code for hot reloading.

## Deployment & Hosting

The site is hosted on **AWS Amplify** with automatic deployments triggered by pushes to the `main` branch on GitHub.

- **GitHub repo:** [WarriorGoat/ncchance-website](https://github.com/WarriorGoat/ncchance-website)
- **Amplify app ID:** `d3il60w0p9ny7b`
- **Amplify URL:** https://main.d3il60w0p9ny7b.amplifyapp.com
- **Custom domain:** https://ncchance.org (and https://www.ncchance.org)
- **SSL:** Amplify-managed certificate (auto-renewing)
- **CDN:** AWS CloudFront (global edge network)

### DNS Configuration

The domain is registered and DNS is managed at **GoDaddy** (nameservers: `ns75.domaincontrol.com` / `ns76.domaincontrol.com`).

**Website records (AWS Amplify / CloudFront):**

| Type | Host | Value |
|------|------|-------|
| CNAME | `www` | `dkgz0e5jt97rn.cloudfront.net` |
| CNAME | `_0acdec370d63e3b4287cab0ecd0d32c9` | `_6c8dd2437e28069edbdb1293608d5a64.jkddzztszm.acm-validations.aws.` |
| Forwarding | `ncchance.org` → `https://www.ncchance.org` | Permanent (301) — GoDaddy Forwarding tab |

> **Note:** GoDaddy does not support ALIAS/ANAME records, so the root domain (`ncchance.org`) uses GoDaddy's built-in domain forwarding to redirect to `https://www.ncchance.org`.

**Email records (Microsoft 365):**

| Type | Host | Value |
|------|------|-------|
| MX | `@` | `ncchance-org.mail.protection.outlook.com` (Priority: 0) |
| CNAME | `autodiscover` | `autodiscover.outlook.com` |
| CNAME | `selector1._domainkey` | `selector1-ncchance-org._domainkey.NCCHANCE.q-v1.dkim.mail.microsoft` |
| CNAME | `selector2._domainkey` | `selector2-ncchance-org._domainkey.NCCHANCE.q-v1.dkim.mail.microsoft` |
| TXT | `@` | `MS=ms32110376` |
| TXT | `@` | `v=spf1 include:spf.protection.outlook.com -all` |

### Deploying Updates

Push any changes to the `main` branch and Amplify will automatically build and deploy within ~1 minute:

```bash
git add .
git commit -m "your message"
git push
```

## Contact

- **Website:** [ncchance.org](https://ncchance.org)
- **Email:** ruth@ncchance.org
- **Executive Director:** Ruth A. Cresenzo, J.D.
- **Mailing Address:** P.O. Box 3013, New Bern, NC 28564
