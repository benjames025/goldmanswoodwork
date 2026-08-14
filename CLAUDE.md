# CLAUDE.md — goldman-brothers

## What this is
A live single-page **brochure site for Goldman's Woodwork** (גולדמנס וודוורק / האחים גולדמן) — brothers **Yosef and David Goldman**, who build custom decks, garden furniture, and bespoke woodwork in **Kibbutz Beit Zera, Israel**. The site is in **Hebrew (RTL)**. Live at **goldmanswoodwork.com**.

Purpose: showcase work (services, gallery, project carousel, pricing intent) and capture leads. There is **no backend** — the request form opens a **pre-filled WhatsApp message** to the brothers' number on submit. This was a deliberate choice (matches "easy way for people to book them", zero infra, and resolved the PII/silent-failure audit findings).

## Stack
- **Plain static HTML** — a single `index.html` with all CSS and JS inline. No framework, no `package.json`, no build step, no test suite.
- **Hosting:** Vercel. `vercel.json` sets `cleanUrls`, a catch-all rewrite to `/index.html` (allow-list excludes the SEO/verification assets), and a full security-header block (HSTS, CSP, X-Frame-Options DENY, X-Content-Type-Options, Referrer-Policy, Permissions-Policy). Push to `main` → Vercel auto-deploys.
- **Lead capture:** client-side only. Form opens `wa.me` with a pre-filled message. Anti-spam = hidden honeypot + 1.5s minimum fill time + phone regex. No remote submission.
- **Assets:** `og-image.jpg` (1200×630), `apple-touch-icon.png`, inline SVG favicon, `sitemap.xml`, `robots.txt`, Google site-verification file. Photos live in **`/img/` as WebP at two sizes** (`<slug>-sm.webp` ~700px for tiles, `<slug>-lg.webp` ~1400px for the lightbox); videos in **`/video/`** as H.264 MP4 (faststart, audio stripped) with a `-poster.webp`. Everything below the fold is lazy-loaded; only the hero image is preloaded. First load ≈ 306 KB.

## Conventions
- **Edit `index.html` directly.** Styles and scripts are inline by design; the CSP permits `'unsafe-inline'` for style/script. Keep it that way.
- **Never re-inline images as base64.** Add new photos as `/img/<slug>-sm.webp` + `-lg.webp` (never upscale past native; encode to a byte budget), and reference them with `loading="lazy"`, `decoding="async"`, explicit `width`/`height`, and `data-full` pointing at the `-lg` file so the lightbox picks it up.
- **Claims in the copy are the brothers' call.** They corrected "covered/slatted ceiling" to **bamboo cladding** and removed all rain-resistance wording (the pergolas give shade, not rain cover). Do not reintroduce weatherproofing claims.
- **Preserve Hebrew / RTL** — keep `dir`/`lang` and the Hebrew copy intact; this is the brothers' primary audience.
- **Never re-introduce a backend form POST or an admin page.** The old `goldman-admin.html` (client-side fake auth + XSS surface) was pulled from the deploy and lives in `old-iterations/` for reference only. Audit findings F-001–F-007 depend on it staying out of the deploy.
- `old-iterations/` = retired snapshots, not served.

## Page order
`hero → recent (newest work, videos) → about → services → gallery → build (process) → carousel → pricing → request → contact`.
The brothers want the **newest work seen immediately**, so `#recent` sits directly under the hero and the hero image is the latest finished job. Keep it that way when adding sections.

## Current focus
Production / maintenance — small brochure site, no formal PLAN.md. Track work in `.agent-shared/HANDOFF.md`.

Closed 2026-08-13: **F-012** (added `404.html`; dropped the catch-all rewrite that returned 200 for every unknown URL), **F-013** (images extracted from base64; 9.58 MB → ~306 KB first load), **F-016** (gallery lightbox wired, with captions, prev/next, keyboard nav and swipe).

Open items:
- **Build-section copy is unverified.** The three "איך אנחנו בונים" step descriptions were written by Claude as a guess at their process and have never been confirmed by Yosef or David. Get their wording.
- **Gallery still repeats photos.** 40 slots were filled by 21 unique images; only the worst duplicates were swapped out. Needs more real photos, not more code.
- Open questions for Yosef/David: email addresses (to add a `mailto:` button beside WhatsApp on form success).

## Gotchas
- The about-section photo strip (`#storyStrip`) uses **native `overflow-x` + scroll-snap**, deliberately. Do not "upgrade" it to a JS drag handler — that is what broke the carousel. Keep `overscroll-behavior-x:contain` (stops a swipe triggering iOS back-navigation). It holds ~6 photos picked for range (pergola, deck, seating, fence, balcony, set); the brothers want variety there, not more of the same category.
- The 3D carousel's touch handling uses **axis detection** (undecided until 8px, then locks to one axis) so a vertical swipe scrolls the page instead of spinning the carousel. This was a real reported bug; do not simplify it back to a plain `touchmove` handler, and keep `touch-action:pan-y` on `.carousel-stage`.
- `overflow-x:hidden` must stay on **`html` as well as `body`** — iOS ignores it on `body` alone.
- The lightbox pins the body and restores `scrollY` on close; without that iOS drops the visitor at the top of the page.

## Origin
Pre-existing site, migrated into dev-hub 2026-05-05 (`.agent-shared/` added for dual-agent coordination). CLAUDE.md authored 2026-05-25 during the CONTEXT.md→CLAUDE.md consolidation (the old `.agent-shared/CONTEXT.md` was an unfilled placeholder; archived to `~/dev-hub/_archive/context-md-migration-2026-05-25/`).
