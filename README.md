# C.H.A.N.C.E. For Crime Victims

**Connecting Help, Advocacy, Navigation, Counsel & Education**

Website for C.H.A.N.C.E., a North Carolina nonprofit dedicated to serving crime victims through legal advocacy, education, and navigation services. The organization's initial project focuses on military-connected survivors of sexual assault, domestic violence, and harassment.

---

## Project Structure

```
ncchance.org/
├── ncchance.html               # Main single-page website
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

The domain is registered at GoDaddy but DNS is managed through **DreamHost** (nameservers: `ns1–ns3.dreamhost.com`). Three records are required in DreamHost:

| Type | Host | Value |
|------|------|-------|
| CNAME | `_0acdec370d63e3b4287cab0ecd0d32c9` | `_6c8dd2437e28069edbdb1293608d5a64.jkddzztszm.acm-validations.aws.` |
| ALIAS | `@` (root) | `dkgz0e5jt97rn.cloudfront.net` |
| CNAME | `www` | `dkgz0e5jt97rn.cloudfront.net` |

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
