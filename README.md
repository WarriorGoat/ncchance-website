# C.H.A.N.C.E. For Crime Victims

**Connecting Help, Advocacy, Navigation, Counsel & Education**

Website for C.H.A.N.C.E., a North Carolina nonprofit providing legal advocacy, navigation, and education for crime victims. The organization's primary initiative focuses on military-connected survivors of sexual assault, domestic violence, and harassment.

---

## Project Structure

```
ncchance.org/
├── index.html                  # Home — hero, mission, donate, contact
├── about.html                  # About — our story, Ruth's bio, Diva Edel, speaking
├── services.html               # Services — victims, professionals, volunteer attorneys
├── blog.html                   # Blog index — Ruth's Truths
├── blog/
│   ├── post-01.html            # Ruth's Truth #1
│   ├── post-02.html            # Ruth's Truth #2
│   └── ... (22 posts total)
├── css/
│   └── chance.css              # Shared styles for blog index and all post pages
├── header.html                 # Shared nav partial (loaded via fetch by blog pages)
├── footer.html                 # Shared footer partial (loaded via fetch by blog pages)
├── images/
│   ├── ChanceLogo_2.svg        # Full organization logo
│   ├── ChanceBadge.svg         # Shield badge (favicon)
│   ├── ruth-cresenzo.jpg       # Executive Director headshot (og:image)
│   └── paypal-qr.png           # PayPal donation QR code
├── docs/
│   └── IRS-501c3-Status-Granting-Letter-2023.pdf
├── sitemap.xml                 # XML sitemap (26 URLs)
├── robots.txt                  # Crawler directives + sitemap reference
├── .gitignore
└── README.md
```

## Page Architecture

The site uses a **multi-page architecture** with two distinct patterns:

**Main pages** (`index.html`, `about.html`, `services.html`) embed their full `<style>` block and nav/footer HTML inline. This avoids the `<script>` execution limitation that affects dynamically injected partials.

**Blog pages** (`blog.html`, `blog/post-*.html`) load `header.html` and `footer.html` via `fetch()` into `<div id="site-header">` and `<div id="site-footer">` placeholders. Shared styles live in `css/chance.css`.

## Tech Stack

- Pure HTML/CSS/JS — no build tools or frameworks
- Multi-page layout: index, about, services, blog index, 22 blog posts
- Google Fonts: DM Sans
- Inline SVG icons (no external icon libraries)
- PayPal Hosted Buttons SDK (`components=hosted-buttons`, button ID `4ANRG7725AZ46`)
- JSON-LD structured data: NGO schema (home), AboutPage + Person (about), WebPage + Offer (services), Blog (blog index), BlogPosting (each post)
- `sitemap.xml` covering all 26 URLs with priority and changefreq
- `robots.txt` allowing all crawlers, disallowing template partials
- Open Graph + Twitter Card meta on all pages
- Responsive design: breakpoints at 900px, 768px, 640px, 600px, 500px

## Design Tokens

| Token | Value | Usage |
|-------|-------|-------|
| `--navy` | `#1a2c4e` | Primary background, headings |
| `--red` | `#b03040` | CTAs, accents |
| `--gold` | `#c4a45a` | Labels, borders, highlights |
| `--beige` | `#f4efe7` | Page background |
| `--font` | DM Sans | All text |

## Development

Open any `.html` file directly in a browser, or use the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) extension in VS Code for hot reloading.

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
- **Phone:** (919) 374-0556
- **Executive Director:** Ruth A. Cresenzo, J.D.
- **Mailing Address:** P.O. Box 3013, New Bern, NC 28564
