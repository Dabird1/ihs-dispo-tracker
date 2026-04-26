# RTAT — Real Time Appointment Tracker

You're working on RTAT, a single-file HTML/CSS/JS PWA at `C:\Users\JayTeresi\Code\ihs-dispo-tracker\`. Jay Teresi is CEO of Infinity Home Services (IHS), a multi-brand home-services holding company (roofing, exteriors, restoration). Demo target: Monday, April 27, 2026.

## The operating problem this solves

IHS runs ~12 brands across multiple markets. Each brand sells in-home appointments (roofing, siding, gutters, etc.). Today, leadership doesn't know how a day, week, or month is shaping up until the close-out happens — and the lag is wildly inconsistent. Sometimes days, sometimes weeks. At the regional and corporate levels, leadership often never sees disposition-level truth at all — it's stuck in 25 different brand workflows that never roll up. By the time numbers do roll up, the day is already lost. Reps walk out of homes and the result lives in their head until they get around to logging it. Branch managers chase reps for dispositions. RVPs run on whatever shows up in the close-out, whenever that lands.

RTAT is the disposition layer on top of the existing CRM (improveit360 / future system). The job: capture every appointment outcome the moment it happens, roll it up cleanly, and put the truth in front of the right person at the right grain — rep sees their day, branch leadership sees their brand, corporate sees the enterprise. No reporting lag, no manager nag, no spreadsheet of spreadsheets.

## Three roles, three views — that's the whole product

- **Rep** (e.g., Karen P. at Infinity Exteriors) — sees only their own appointments, locked to their brand. Their landing page on mobile is the disposition queue: what's next, dispo it, move on. No filters — scope is fixed. Pills: Today + Yesterday only.
- **Brand Leadership** (Sales Manager / Branch Manager / Brand President) — sees everything in their brand. Can disposition on behalf of a rep when needed. Full time-frame pills (Today, WTD, LWK, MTD, QTD, Custom).
- **Corporate** (RVP through enterprise) — view-only, all brands, all markets, all reps. Drills into by-brand, by-market, by-rep. Same time-frame pills.

That's it. Earlier drafts had six personas; we collapsed to three because more granular RBAC was making the demo harder to read without giving anyone meaningful new power.

## What's intentionally NOT here yet

- No real backend. Submissions live in `localStorage` under `ihs_dispo_subs_v2`. Persona is mock-switched, not auth'd.
- No real SSO. Production target is Azure AD; for now the persona dropdown is the SSO stand-in.
- No CRM integration. All appointments and submissions are seeded on first load via `seedDemoHistory()` — ~80 days of narrative-shaped data so every view reads as real (Infinity Exteriors healthy, Premier Roofing slipping, GF Sprague high-end, Skywalker struggling; Kenosha hot, Columbus cool; Karen P. outperforms, Doug M. lags).
- No comparison lines on KPI tiles yet — that's v12.

## Architecture (and why)

One file. `index.html` has all CSS and JS embedded. `sw.js` is a minimal cache-first service worker. `manifest.webmanifest` makes it installable. Hosted on GitHub Pages. The whole thing is ~110 KB. This is deliberate — Jay can demo from any iPhone or Android by visiting the URL, and the install-to-home-screen flow looks like a real app. We optimize for time-to-truth, not engineering elegance.

## Patch convention

Don't hand-edit `index.html` for non-trivial changes. Use the Python patch script pattern: `outputs/patch_rtat_vN.py` reads the file, runs `html.replace(old, new)` with a `count == expected` assertion for each change, and writes back. Bump `CACHE_NAME` in `sw.js` (`rtat-vN`) every release. Lint the embedded JS with `node --check` before pushing. This catches drift fast and gives a clean changelog.

## Brand constraints (IHS)

Primary `#2CB2DF`, secondary `#1A89AE`, dark font `#666666`, good `#67A64B`, bad `#A02121`, light `#E8E8E8`. Body type leans Aptos Narrow at 11pt; section headers Aptos Display 16pt bold all caps. Already wired into the CSS variables.

## How Jay works

CEO. 30 years in retail, home services, manufacturing, M&A, transformation. Wants the truth, fast, with tradeoffs called out — not a McKinsey-flavored hedge. When you give an answer, invert first: lead with the strongest case it fails. Plain language. No jargon for its own sake. He'll tell you what's wrong and you fix it; he doesn't need encouragement.

## UX principles in use

Don't Make Me Think (Krug). One obvious next action. Mobile-first for reps. Reps walk out of a home and dispo before the truck moves; every tap that isn't necessary loses a rep.

## Current state (as of v13)

- v11: narrative demo data, real time-frame pill filtering, rep desktop filterbar hidden, rep-first mobile layout
- v12: GM tiers anchored on 42.5% AOP plan, stuck SLA tiers (90/120/240 min), pill+button count badges, contextual empty states, inline date-range popover, slide-in success card, keyboard nav, sticky mobile CTA, ARIA pass
- v13: seed leaves indexes 1, 2, 6, 10 un-dispositioned so all stuck tiers visibly demo

Next up: comparison lines on KPI tiles (vs prior period, pace vs plan), and Tier 3 polish (persona tour, skeletons, real date-picker library).
