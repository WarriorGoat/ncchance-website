# ncchance.org — Domain Migration Plan: GoDaddy → AWS Route 53

**Prepared:** February 2026
**Domain:** ncchance.org
**Registrar (current):** GoDaddy (nameservers: ns75.domaincontrol.com / ns76.domaincontrol.com)
**Registrar (target):** AWS Route 53
**Hosting:** AWS Amplify (already on AWS — no change)
**Email:** Microsoft 365 (already on M365 — no change to mail service, only DNS records move)
**SSL:** Amplify-managed ACM certificate (already on AWS — no change)

---

## Why Move?

- **Consolidation:** Domain, DNS, hosting, CDN, and SSL will all live in one AWS account — no separate GoDaddy login
- **Root domain fix:** GoDaddy doesn't support ALIAS/ANAME records, forcing the current hacky 301 forwarding workaround for `ncchance.org → www.ncchance.org`. Route 53 natively supports ALIAS records, so the root domain can point directly to CloudFront with no redirect penalty
- **Better TTL control:** Route 53 gives full TTL control on every record, unlike GoDaddy's locked-down interface
- **Auto-renewal stays in AWS billing:** No separate GoDaddy renewal reminders

---

## Route 53 Hosted Zone (Created ✅)

- **Hosted Zone ID:** `Z0457513291UJIXDIQM14`
- **Status:** Active — all records loaded
- **Route 53 Nameservers (for domain transfer):**
  - `ns-125.awsdns-15.com`
  - `ns-1601.awsdns-08.co.uk`
  - `ns-1421.awsdns-49.org`
  - `ns-610.awsdns-12.net`

---

## Current DNS Records to Migrate

All of these are now recreated in Route 53. ✅

### Website Records

| Type | Host | Value | Notes |
|------|------|-------|-------|
| CNAME | `www` | `dkgz0e5jt97rn.cloudfront.net` | Amplify/CloudFront |
| CNAME | `_0acdec370d63e3b4287cab0ecd0d32c9` | `_6c8dd2437e28069edbdb1293608d5a64.jkddzztszm.acm-validations.aws.` | ACM cert validation |
| **ALIAS (A + AAAA)** | `@` (root) | `dkgz0e5jt97rn.cloudfront.net` | **Replaces GoDaddy 301 forwarding — resolves directly, no redirect** |

### Email Records (Microsoft 365)

| Type | Host | Value | Priority |
|------|------|-------|----------|
| MX | `@` | `ncchance-org.mail.protection.outlook.com` | 0 |
| CNAME | `autodiscover` | `autodiscover.outlook.com` | — |
| CNAME | `selector1._domainkey` | `selector1-ncchance-org._domainkey.NCCHANCE.q-v1.dkim.mail.microsoft` | — |
| CNAME | `selector2._domainkey` | `selector2-ncchance-org._domainkey.NCCHANCE.q-v1.dkim.mail.microsoft` | — |
| TXT | `@` | `MS=ms32110376` | — |
| TXT | `@` | `v=spf1 include:spf.protection.outlook.com -all` | — |

> ⚠️ Route 53 supports multiple TXT records on the same host — both TXT entries above can coexist.

---

## Migration Plan

### Phase 1 — Create Route 53 Hosted Zone and Replicate DNS ✅ COMPLETE

**Completed: February 24, 2026**

Hosted zone `Z0457513291UJIXDIQM14` created and all 12 records loaded via AWS API:
- Root A + AAAA ALIAS → CloudFront (replaces GoDaddy 301 forwarding)
- www A + AAAA ALIAS → CloudFront
- ACM cert validation CNAME
- MX → Microsoft 365
- autodiscover CNAME, selector1/selector2 DKIM CNAMEs
- SPF + MS domain verification TXT records
- NS + SOA (auto-created by Route 53)

### Phase 2 — Unlock Domain and Get Authorization Code (GoDaddy)

**Estimated time: 10 minutes | Do this after Phase 1 is complete**

1. Log in to GoDaddy → **My Products → Domains → ncchance.org → Settings**
2. Under **Domain Lock**, toggle to **Unlocked**
3. Under **Transfer Away from GoDaddy**, click **Get Authorization Code**
4. GoDaddy will email the EPP/auth code to the account email address on file
5. Copy and save the authorization code — it expires in 5–7 days

> ⚠️ The domain must have been registered (or last transferred) more than 60 days ago to be eligible for transfer. Given the site's history, this should not be an issue.

### Phase 3 — Initiate Domain Transfer in Route 53

**Estimated time: 15 minutes to initiate | Transfer takes 5–7 days**

1. AWS Console → **Route 53 → Domains → Transfer domain**
2. Enter `ncchance.org` → check availability (it will show "transferable")
3. Enter the **authorization code** from Phase 2
4. Choose **Use Route 53 hosted zone** (select the one created in Phase 1)
5. Confirm contact/WHOIS information
6. Select **Auto-renew** (recommended)
7. Pay the transfer fee (~$13/year for .org, prorated)
8. Submit

### Phase 4 — Approve the Transfer

**Estimated time: varies, usually within 24 hours of initiating**

- GoDaddy will send a **transfer approval email** to the domain's registrant email address
- Click **Approve** in that email (if you don't, GoDaddy will auto-approve after 5 days)
- AWS will also send a confirmation when transfer completes

> ✅ When the transfer completes, Route 53 **automatically becomes the authoritative nameserver** — no separate nameserver update step needed. The hosted zone from Phase 1 takes over automatically.

### Phase 5 — Post-Transfer Verification

**Do these checks within 24 hours of transfer completing**

- [ ] `https://ncchance.org` loads the site (ALIAS record, no redirect)
- [ ] `https://www.ncchance.org` loads the site (CNAME to CloudFront)
- [ ] Send a test email to `ruth@ncchance.org` and confirm delivery
- [ ] Send a test email **from** `ruth@ncchance.org` and confirm it doesn't land in spam (SPF/DKIM check)
- [ ] SSL cert is green in browser (ACM cert still valid — no action needed)
- [ ] Check Route 53 hosted zone — all records present
- [ ] Run `dig ncchance.org NS` and confirm Route 53 nameservers are returned

### Phase 6 — Clean Up GoDaddy

After verifying everything works:

1. Cancel GoDaddy domain forwarding (it's no longer in use — Route 53 ALIAS handles root)
2. Confirm GoDaddy has no remaining active subscriptions (email hosting, SSL, forwarding)
3. Optionally close or keep the GoDaddy account (no recurring charges should remain)

---

## Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Email downtime during nameserver switch | Low | High | All M365 records recreated in Phase 1 before transfer completes; TTL 300s |
| SSL cert interruption | Very low | High | ACM cert is already issued and managed by Amplify; validation CNAME migrated in Phase 1 |
| Website downtime | Very low | High | All site DNS records recreated before transfer; ALIAS eliminates GoDaddy forwarding dependency |
| Transfer rejected (domain locked/ineligible) | Very low | Medium | Unlock domain in Phase 2; verify 60-day eligibility |
| Authorization code expires before use | Low | Low | Submit transfer (Phase 3) the same day you get the auth code (Phase 2) |

---

## Architecture Improvement: Root Domain ALIAS

The current setup uses **GoDaddy domain forwarding** (a 301 HTTP redirect) to handle the root domain:

```
ncchance.org → 301 → https://www.ncchance.org → CloudFront → Amplify
```

After migration, Route 53 ALIAS allows the root domain to resolve directly:

```
ncchance.org → CloudFront (dkgz0e5jt97rn.cloudfront.net) → Amplify
```

This eliminates an extra redirect hop, is better for SEO, and makes both `ncchance.org` and `www.ncchance.org` first-class addresses.

---

## Cost Estimate

| Service | Cost |
|---------|------|
| Route 53 hosted zone | $0.50/month ($6/year) |
| Route 53 domain registration (.org) | ~$13/year |
| Route 53 DNS queries | ~$0.01–0.05/month (negligible traffic) |
| **Total** | **~$19–20/year** |

GoDaddy .org domain registration is typically $15–22/year at renewal, so this is roughly cost-neutral, with the consolidation benefit.

---

## Reference

- Amplify App ID: `d3il60w0p9ny7b`
- CloudFront distribution: `dkgz0e5jt97rn.cloudfront.net`
- ACM validation CNAME (host): `_0acdec370d63e3b4287cab0ecd0d32c9`
- ACM validation CNAME (value): `_6c8dd2437e28069edbdb1293608d5a64.jkddzztszm.acm-validations.aws.`
- Microsoft 365 MX: `ncchance-org.mail.protection.outlook.com` (priority 0)
