# CLAUDE.md — the-good-foundry public site

Notes for future Claude Code sessions working on this repo so
patterns established at carve-out time don't get accidentally
regressed.

---

## Scope

This repo serves `www.the-good-foundry.com` ONLY. The WBM application
(bank, vault, evaluation, rep portal, admin, ALL of `api/`) lives in
the sister repo at `c:\Users\marty\OneDrive\Desktop\00 WM Development`
and deploys to `smart-tools-foundry.com/WBM/`.

**Do not move app/admin/API code into this repo.** The split is
intentional and the boundary is the firewall — touching it usually
means a different repo is the right place to work.

## What lives here vs what stays at smart-tools-foundry.com

| Lives here | Stays at WBM |
|---|---|
| Marketing + legal HTML | `wm_bank`, `wm_vault`, `wm_evaluation` |
| Surveys (aging-well, livewell, permah-free) | `rep_portal`, `rep_wallet` |
| Token-gated outbound (feedback, offer, proposal, renewal_response, kids_360, approve_power_down) | All sign-in pages |
| Static assets (logos, images) — copies, NOT canonical | `redeem.html` (printed QR codes lock the URL) |
| | `api/`, all PHP, all DB code |
| | OAuth callbacks at `/WBM/api/oauth/*` |
| | SMTP infra at mail.smart-tools-foundry.com |

## Stack

- Static HTML/CSS/JS — no build step
- SiteGround shared hosting, dedicated account for this domain
- GitHub Actions deploy via lftp FTPS-explicit (port 21 + AUTH TLS)
- Apex `the-good-foundry.com` AND `www` both serve directly (no 301
  apex→www redirect — Marty's call 2026-06-20)

## Cross-origin to WBM API

Token-gated pages POST back to `https://smart-tools-foundry.com/WBM/api/*`.
That host's CORS allowlist in `api/wm_bootstrap.php` (WBM repo) must
include both `https://www.the-good-foundry.com` and
`https://the-good-foundry.com`. CORS extension lives in WBM, NOT here.

## Outbound URL helper (WBM-side)

Emails sent from WBM build public-page URLs via `wm_public_url($path)`
in `api/wm_bootstrap.php` (WBM repo). The helper reads
`WM_PUBLIC_BASE_URL` from `wm_secrets.php` above the WBM webroot:

```
WM_PUBLIC_BASE_URL=https://www.the-good-foundry.com
```

When set, links route here. When unset/empty, links fall back to the
legacy `/WBM/app/X.html` shape — zero-behaviour-change default.

To rotate the domain: edit `wm_secrets.php`, no code change needed.

## Assets

Asset references (logos, fonts, hero images) are absolute URLs to
`https://smart-tools-foundry.com/WBM/app/logos/...` etc. so the
canonical asset library stays in one place. Do NOT duplicate logos
into this repo — when a client logo or platform asset changes, it
should update once in WBM and propagate everywhere.

## Vanity URLs

The `.htaccess` at `public/.htaccess` rewrites `/business-case` →
`/business-case.html` (and so on) for cleaner share/SEO URLs. Don't
hard-code `.html` suffixes in nav links.

## Deploy

- Push to `main` → GitHub Actions runs `lftp mirror -R --delete`
  against the SG account
- Source: `./public/`
- Target: `/public_html/` on the new SG account
- Cache: SG edge cache may hold stale HTML after deploy. If
  curl-from-outside shows the new file but the browser doesn't, flush
  dynamic cache from SG Hosting Panel → Speed → Caching. Don't loop
  on browser-side cache busts.

## What NOT to do

- Don't add PHP — this is a static site
- Don't add a database
- Don't add the WBM CORS allowlist here (it belongs in WBM's
  `wm_bootstrap.php`)
- Don't duplicate logos/assets — reference them absolute to WBM
- Don't bypass the lftp deploy with manual FTP uploads
- Don't move `redeem.html` here (printed QR codes lock that URL)
