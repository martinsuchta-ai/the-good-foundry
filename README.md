# the-good-foundry — public marketing site

Static-site repo for `www.the-good-foundry.com`.

Houses the public-facing marketing, legal, survey, and token-gated
outbound pages that were previously served from
`smart-tools-foundry.com/WBM/app/`. Carved out for SEO + brand
separation so the app surface and the marketing surface can evolve
independently.

## What lives here

- **Marketing**: landing page, business case, bookcall, explorer,
  offerings, backed-by-science, technical-faq
- **Legal**: EULAs, acknowledgement, mutual confidentiality,
  subprocessors
- **Surveys**: aging-well, livewell, livewell-vs-aging-well,
  permah-free, livewell-flourishing-together
- **Outbound token-gated**: feedback, offer, proposal, renewal
  response, approve-power-down, kids_360

Bank, vault, evaluation, rep portal, sign-in pages, and ALL of the
`api/` surface stay at `smart-tools-foundry.com/WBM/`. So does
`redeem.html` — moving it would break every printed QR sticker.

## Stack

- Static HTML / CSS / JS — no build step, no framework
- SiteGround shared hosting (separate account from
  `smart-tools-foundry.com`)
- GitHub Actions deploy via `lftp mirror --delete` over FTPS

## Cross-origin API calls

The token-gated pages and surveys POST to
`https://smart-tools-foundry.com/WBM/api/*`. That host's CORS
allowlist in `wm_bootstrap.php` must include
`https://www.the-good-foundry.com` and
`https://the-good-foundry.com`.

## Outbound URL generation

Emails sent from WBM (proposals, renewal reminders, paid item
follow-ups, key retrieval, penny-for-thoughts) build link URLs via
the `wm_public_url()` helper in WBM's `api/wm_bootstrap.php`. The
helper reads `WM_PUBLIC_BASE_URL` from `wm_secrets.php` — set to
`https://www.the-good-foundry.com` to route outbound links here;
leave unset to fall back to legacy `/WBM/app/X.html` shape.

## Deploy

Push to `main` → GitHub Actions runs `lftp mirror --delete` against
the new SG account. See [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml)
for the FTPS-explicit pattern + exclude list.

Required GitHub Actions secrets:
- `FTP_HOST` — usually the SG FTP hostname (NOT the domain)
- `FTP_USER` — full user@domain form on shared hosting
- `FTP_PASS` — single-quoted in secrets to avoid `$` interpolation
