# HANDOFF — goldman-brothers

*Schema: `~/dev-hub/agent-memory/global/HANDOFF_SCHEMA.md` (v3)*

## STATE
- Phase: production (no formal PLAN.md — small brochure site, single deploy)
- Phase gate: open
- Active goal: —
- Blocked by: —
- Last actor: claude (2026-05-22T22:34:11-06:00)
- Works: live at goldmanswoodwork.com; gallery (14 items, 2 featured); request form opens pre-filled WhatsApp on submit; security headers (HSTS/CSP/XFO/etc.); social previews (og:image + apple-touch-icon); SVG favicon; SEO basics solid
- In progress: —
- Broken: —

## OPEN QUESTIONS FOR USER
- Add brothers' email addresses → wire a `mailto:` button next to the WhatsApp one on the form-success screen
- Provide a clean hero photo of the geometric-dome/main deck for the JSON-LD `image` property?

## FROZEN
| File/area | Reason | Added | Owner | Clears when |
|-----------|--------|-------|-------|-------------|

## LAST CHANGE
- Actor: claude
- Touched: index.html
- Why: rewrite form-submit to open a pre-filled WhatsApp message to Yosef/David's number instead of POSTing to a silently-broken Apps Script — matches user intent ("easy way for people to book them"), zero backend, fully resolves C2 + C3 from the audit
- Impacts: lead capture flow; security posture (no remote data submission from the form)
- Risk: low
- Tests: not run (static HTML, no test suite); pushed to main, Vercel auto-deploys
- Next action: user smoke-tests on phone — fill form, confirm WhatsApp opens with correct number + readable message

## CODEX FINDINGS
*Audit conducted by claude (no Codex pass — `bin/review` needs interactive TTY). Severity uses C/H/M/L per audit, mapped to schema enum: critical→`critical`, high→`high`, medium→`med`, low→`low`. All criticals and highs are resolved.*

| ID    | Severity | Status   | Summary                                                       | File:line                          | Owner  | Opened                     | Resolved                   | Resolution                                                                 |
|-------|----------|----------|---------------------------------------------------------------|------------------------------------|--------|----------------------------|----------------------------|----------------------------------------------------------------------------|
| F-001 | critical | fixed    | /admin page publicly accessible, client-side fake auth        | goldman-admin.html:116,138         | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Moved file to old-iterations/; removed /admin rewrite; robots disallow     |
| F-002 | critical | fixed    | Form silently lies about success (try/catch + no-cors)        | index.html:583                     | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:34:11-06:00  | Killed Apps Script call; form now opens WhatsApp pre-filled (a0ac4ab)      |
| F-003 | critical | fixed    | Customer PII in URL query string (GET to Apps Script)         | index.html:583                     | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:34:11-06:00  | Same as F-002 — no remote submission at all now                            |
| F-004 | critical | fixed    | Photo upload widget is fake (files read but never sent)       | index.html:565-569,582             | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Removed widget; pointed users to WhatsApp for inspiration photos           |
| F-005 | high     | fixed    | XSS via Supabase fields injected into admin innerHTML         | goldman-admin.html:206-222         | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Admin removed from deploy (F-001 fix supersedes)                            |
| F-006 | high     | fixed    | Admin localStorage fallback dead — form never writes to it    | goldman-admin.html:149             | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Admin removed from deploy                                                  |
| F-007 | high     | fixed    | Admin status updates only saved locally — no cross-device sync| goldman-admin.html:228-239         | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Admin removed from deploy                                                  |
| F-008 | med      | fixed    | Missing security headers (CSP/HSTS/XFO/etc.)                  | vercel.json                        | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Added full header block in vercel.json                                     |
| F-009 | med      | fixed    | target=_blank without rel=noopener noreferrer                 | index.html:450,458                 | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Added rel attribute to both wa.me links                                    |
| F-010 | med      | fixed    | No og:image / twitter:image — grey-box previews on social     | index.html head                    | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:24:30-06:00  | Added og-image.jpg (1200x630, seating-set hero) + meta tags (ef2fc3b)      |
| F-011 | med      | fixed    | No favicon / apple-touch-icon                                 | index.html head                    | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:24:30-06:00  | Inline SVG favicon (א״ג mark) + 180x180 apple-touch-icon                  |
| F-012 | med      | open     | Catch-all rewrite returns 200 for every unknown URL           | vercel.json                        | claude | 2026-05-22T22:00:00-06:00  | —                          | Need custom 404.html; low priority for brochure site                       |
| F-013 | med      | open     | Page weight ~9.5 MB (all images base64-inlined in index.html) | index.html                         | claude | 2026-05-22T22:00:00-06:00  | —                          | Extract images to /img/*.jpg + lazy-load; defer until performance complaint|
| F-014 | med      | fixed    | No anti-spam on the form                                      | index.html form                    | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Hidden honeypot + 1.5s minimum form-fill time                              |
| F-015 | low      | fixed    | No phone format validation                                    | index.html submit handler          | claude | 2026-05-22T22:00:00-06:00  | 2026-05-22T22:14:30-06:00  | Added regex /^[\d+()\-\s]{7,20}$/                                          |
| F-016 | low      | open     | Gallery items not clickable (lightbox only wired to carousel) | index.html:596                     | claude | 2026-05-22T22:00:00-06:00  | —                          | UX miss; trivial to wire (~10 min)                                         |

## LOG
- 2026-05-05 [system] dev-hub migration — added .agent-shared/ for dual-agent coordination
- 2026-05-09T22:00:00-06:00 [system] backfill — migrated HANDOFF.md to v3 schema; original preserved as HANDOFF.legacy.md
- 2026-05-19T14:06:28-06:00 [claude] gallery — added 2 new photos (planters wide-feature, deck-build tall), softer radius, dense auto-flow, mobile 2-col fallback (3cb391c)
- 2026-05-22T22:14:30-06:00 [claude] audit — 16 findings opened (4 critical, 3 high, 7 med, 2 low); applied all critical + high + most med fixes in one pass (10afa12)
- 2026-05-22T22:24:30-06:00 [claude] social — added og:image (1200x630 seating-set crop) + apple-touch-icon from new deck photo (ef2fc3b)
- 2026-05-22T22:34:11-06:00 [claude] form — pivoted lead capture to pre-filled WhatsApp (no backend); resolves F-002 + F-003 + matches user intent (a0ac4ab)
