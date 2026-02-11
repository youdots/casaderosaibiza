# AGENTS-MENU.md

## Purpose
Server-side runbook for menu URL stability and SEO control.

Goals:
- old menu links keep working
- site always serves only the latest EN/ES menus
- only canonical menu URLs are indexed

Note:
- `-BW.pdf` variants are discontinued and should not be expected going forward.

## Canonical URL Strategy
Use only these public menu URLs:
- `/Casa-de-Rosa-Ibiza-Menu-EN.pdf`
- `/Casa-de-Rosa-Ibiza-Menu-ES.pdf`

These canonical URLs must return `200` directly (not redirect).

## Server Implementation

### 1) Nginx routing
Apply on both staging and live virtual hosts.

- Canonical URLs: serve latest file (via symlink/copy target).
- Old dated URLs: `301` to canonical.

Suggested redirects:
- any legacy `CdR-Menu-YYYY-MM-DD-EN.pdf` -> `/Casa-de-Rosa-Ibiza-Menu-EN.pdf`
- any legacy `CdR-Menu-YYYY-MM-DD-ES.pdf` -> `/Casa-de-Rosa-Ibiza-Menu-ES.pdf`
- any dated `Casa-de-Rosa-Ibiza-Menu-YYYY-MM-DD-EN.pdf` -> `/Casa-de-Rosa-Ibiza-Menu-EN.pdf`
- any dated `Casa-de-Rosa-Ibiza-Menu-YYYY-MM-DD-ES.pdf` -> `/Casa-de-Rosa-Ibiza-Menu-ES.pdf`

No `-BW` redirect rules are needed for future rollouts.

### 2) Deploy script behavior
Update deploy scripts (`/usr/local/bin/deploy-casaderosaibiza` and staging counterpart) to atomically repoint canonical files to newest dated files.

Example logic:
1. Find newest EN file matching `Casa-de-Rosa-Ibiza-Menu-*-EN.pdf`.
2. Find newest ES file matching `Casa-de-Rosa-Ibiza-Menu-*-ES.pdf`.
3. Fail deployment if either is missing.
4. Update canonical targets atomically:
   - `Casa-de-Rosa-Ibiza-Menu-EN.pdf`
   - `Casa-de-Rosa-Ibiza-Menu-ES.pdf`

Use symlinks (`ln -sfn`) or copy+rename atomically.

### 3) Caching headers
- Canonical URLs: `Cache-Control: no-cache` (or very short TTL) so newest menu is always fetched.
- Dated files: long cache (`public, max-age=31536000, immutable`) if directly requested.

## SEO Implementation

### 4) Indexing policy
- Only canonical menu URLs should appear in sitemap.
- Dated menu URLs should not appear in sitemap.
- Prefer redirects over robots blocking for old URLs, so crawlers can consolidate signals.

## Validation Checklist
Run after deploy on staging and live:

1. Canonical EN returns 200:
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-EN.pdf`

2. Canonical ES returns 200:
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-ES.pdf`

3. Old dated EN redirects to canonical:
- `curl -I https://<host>/CdR-Menu-2026-01-26-EN.pdf`

4. Old dated ES redirects to canonical:
- `curl -I https://<host>/CdR-Menu-2026-01-26-ES.pdf`

5. Any dated Casa-de-Rosa URL redirects to canonical:
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-2026-02-09-EN.pdf`
- `curl -I https://<host>/Casa-de-Rosa-Ibiza-Menu-2026-02-09-ES.pdf`

Expected redirects:
- `301` with `Location: /Casa-de-Rosa-Ibiza-Menu-EN.pdf` or `/Casa-de-Rosa-Ibiza-Menu-ES.pdf`

## Ongoing Two-Week Menu Update Process
1. Add new dated EN/ES PDFs.
2. Deploy.
3. Deploy script repoints canonical EN/ES PDFs.
4. Verify checklist above.
5. Keep sitemap canonical-only.
