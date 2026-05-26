# CLAUDE.md — goldman-brothers

## What this is
A live single-page **brochure site for Goldman's Woodwork** (גולדמנס וודוורק / האחים גולדמן) — brothers **Yosef and David Goldman**, who build custom decks, garden furniture, and bespoke woodwork in **Kibbutz Beit Zera, Israel**. The site is in **Hebrew (RTL)**. Live at **goldmanswoodwork.com**.

Purpose: showcase work (services, gallery, project carousel, pricing intent) and capture leads. There is **no backend** — the request form opens a **pre-filled WhatsApp message** to the brothers' number on submit. This was a deliberate choice (matches "easy way for people to book them", zero infra, and resolved the PII/silent-failure audit findings).

## Stack
- **Plain static HTML** — a single `index.html` with all CSS and JS inline. No framework, no `package.json`, no build step, no test suite.
- **Hosting:** Vercel. `vercel.json` sets `cleanUrls`, a catch-all rewrite to `/index.html` (allow-list excludes the SEO/verification assets), and a full security-header block (HSTS, CSP, X-Frame-Options DENY, X-Content-Type-Options, Referrer-Policy, Permissions-Policy). Push to `main` → Vercel auto-deploys.
- **Lead capture:** client-side only. Form opens `wa.me` with a pre-filled message. Anti-spam = hidden honeypot + 1.5s minimum fill time + phone regex. No remote submission.
- **Assets:** `og-image.jpg` (1200×630), `apple-touch-icon.png`, inline SVG favicon, `sitemap.xml`, `robots.txt`, Google site-verification file. Gallery/project images are currently **base64-inlined** into `index.html` (~9.5 MB page weight — see open finding F-013).

## Conventions
- **Edit `index.html` directly.** Styles and scripts are inline by design; the CSP permits `'unsafe-inline'` for style/script. Keep it that way unless extracting assets (then update the CSP).
- **Preserve Hebrew / RTL** — keep `dir`/`lang` and the Hebrew copy intact; this is the brothers' primary audience.
- **Never re-introduce a backend form POST or an admin page.** The old `goldman-admin.html` (client-side fake auth + XSS surface) was pulled from the deploy and lives in `old-iterations/` for reference only. Audit findings F-001–F-007 depend on it staying out of the deploy.
- `old-iterations/` = retired snapshots, not served.

## Current focus
Production / maintenance — small brochure site, no formal PLAN.md. Track work in `.agent-shared/HANDOFF.md` (it carries the full audit-findings table). Open items as of the migration:
- **F-012** — catch-all rewrite returns 200 for unknown URLs; add a custom `404.html` (low priority).
- **F-013** — extract base64 images to `/img/*.jpg` + lazy-load to cut page weight (defer until a perf complaint).
- **F-016** — gallery items aren't clickable; wire the lightbox (lightbox currently only on the carousel, ~10 min).
- Open questions for Yosef/David: email addresses (to add a `mailto:` button beside WhatsApp on form success) and a clean hero photo for the JSON-LD `image` property.

## Origin
Pre-existing site, migrated into dev-hub 2026-05-05 (`.agent-shared/` added for dual-agent coordination). CLAUDE.md authored 2026-05-25 during the CONTEXT.md→CLAUDE.md consolidation (the old `.agent-shared/CONTEXT.md` was an unfilled placeholder; archived to `~/dev-hub/_archive/context-md-migration-2026-05-25/`).
