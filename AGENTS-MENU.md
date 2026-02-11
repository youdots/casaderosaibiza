# AGENTS-MENU.md

## Purpose
Operational runbook for stable menu URLs, safe rollouts, and SEO consistency on:
- `https://casaderosaibiza.com` (live)
- `https://staging.casaderosaibiza.com` (staging)

Primary goals:
- all old menu links keep working
- only canonical EN/ES menu URLs are presented publicly
- live indexing points to canonical URLs only

## Canonical URL Strategy
Public canonical menu URLs:
- `/Casa-de-Rosa-Ibiza-Menu-EN.pdf`
- `/Casa-de-Rosa-Ibiza-Menu-ES.pdf`

Canonical requirements:
- return `200` with `Content-Type: application/pdf`
- must not redirect
- must not fall back to `index.html`

## Legacy URL Compatibility
Legacy dated URLs must `301` to canonical:
- `CdR-Menu-YYYY-MM-DD-EN.pdf`
- `CdR-Menu-YYYY-MM-DD-ES.pdf`
- `CdR-Menu-YYYY-MM-DD-EN-BW.pdf`
- `CdR-Menu-YYYY-MM-DD-ES-BW.pdf`
- `Casa-de-Rosa-Ibiza-Menu-YYYY-MM-DD-EN.pdf`
- `Casa-de-Rosa-Ibiza-Menu-YYYY-MM-DD-ES.pdf`

## Nginx Rules (Live + Staging)
Implement in both virtual hosts before generic SPA fallback:

1) Canonical endpoints as exact matches (`location =`) with `try_files ... =404`.
2) Legacy regex locations that return `301` to canonical EN/ES paths.
3) Generic `location /` remains SPA fallback (`try_files $uri $uri/ /index.html`).

Caching policy:
- canonical menu URLs: `Cache-Control: no-cache, must-revalidate`
- dated files (if served directly outside redirects): `Cache-Control: public, max-age=31536000, immutable`

Staging indexing policy:
- keep `X-Robots-Tag: noindex, nofollow, noarchive`
- keep `robots.txt` as `Disallow: /`

## Deploy Script Rules
Scripts:
- `/usr/local/bin/deploy-casaderosaibiza`
- `/usr/local/bin/deploy-casaderosaibiza-staging`

After rsync, scripts must atomically repoint canonical files:
1) Resolve latest EN and ES source file.
   - prefer `Casa-de-Rosa-Ibiza-Menu-YYYY-MM-DD-(EN|ES).pdf`
   - fallback to legacy `CdR-Menu-YYYY-MM-DD-(EN|ES).pdf`
2) Fail deployment if EN or ES source is missing.
3) Atomically update:
   - `Casa-de-Rosa-Ibiza-Menu-EN.pdf`
   - `Casa-de-Rosa-Ibiza-Menu-ES.pdf`

Preferred implementation:
- symlink + atomic rename (`ln -s` to temp name, then `mv -Tf`)

## Repo Content Rules
- `index.html` should link to canonical menu URLs (not dated URLs).
- `sitemap.xml` should list canonical menu URLs only.
- live `robots.txt` should not block legacy menu URLs; rely on redirects for consolidation.

## Validation Checklist
Run after each deploy on both hosts.

Canonical behavior:
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-EN.pdf`
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-ES.pdf`
- expect: `200`, `Content-Type: application/pdf`, no redirect

Legacy redirect behavior:
- `curl -I https://<host>/CdR-Menu-2026-01-26-EN.pdf`
- `curl -I https://<host>/CdR-Menu-2026-01-26-ES.pdf`
- `curl -I https://<host>/CdR-Menu-2026-01-26-EN-BW.pdf`
- `curl -I https://<host>/CdR-Menu-2026-01-26-ES-BW.pdf`
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-2026-02-09-EN.pdf`
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-2026-02-09-ES.pdf`
- expect: `301` to canonical EN/ES URL

SEO checks:
- live `sitemap.xml` includes canonical menu URLs only
- staging includes `X-Robots-Tag: noindex, nofollow, noarchive`

## Update Workflow
1) Add new dated EN/ES PDFs to repository.
2) Keep canonical menu references in `index.html` unchanged.
3) Deploy staging, run checklist, then deploy live.
4) Confirm canonical files now point to latest dated EN/ES sources.
5) Keep a short release note with date and menu source files.
