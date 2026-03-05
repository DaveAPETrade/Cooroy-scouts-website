---
phase: 02-content-pages
plan: "03"
subsystem: ui
tags: [astro, tailwind, web3forms, hcaptcha, contact-form, content-pages]

# Dependency graph
requires:
  - phase: 02-content-pages
    plan: "01"
    provides: "Brand tokens (scout-maroon, scout-navy, scout-gold, section colors), Nav with official logo, Footer, BaseLayout"
provides:
  - About page at /about with group background, scouting explanation, 3-value grid, and join CTA (ABUT-01)
  - Events/Term Program page at /events with CMS-compatible termProgram data structure (EVNT-01)
  - Contact/Join page at /join with Web3Forms form, hCaptcha spam protection (CONT-01, CONT-02, CONT-03)
affects:
  - 03-gallery (all nav links now resolve — /about, /events, /join are live)
  - 04-cms-integration (termProgram array in events.astro uses CMS-compatible data structure for seamless Phase 4 migration)

# Tech tracking
tech-stack:
  added:
    - "Web3Forms (external SaaS) — contact form email forwarding via fetch POST to api.web3forms.com/submit"
    - "hCaptcha (via Web3Forms client script) — bot protection rendered by web3forms.com/client/script.js"
  patterns:
    - "Web3Forms form pattern: hidden access_key + subject + from_name fields, honeypot botcheck, h-captcha div, client script"
    - "Fetch-based form submission with e.preventDefault() for in-page success/error state without navigation"
    - "termProgram array in frontmatter: { term, dates, entries: [{ date, activity, sections }] } — matches Phase 4 CMS content collection schema"

key-files:
  created:
    - src/pages/about.astro
    - src/pages/events.astro
    - src/pages/join.astro
  modified: []

key-decisions:
  - "termProgram entries use sections (string) not section (enum) to allow 'All sections' and multi-section values — maps cleanly to a CMS text field in Phase 4"
  - "Form submits via fetch POST (not native form POST) so success/error can be shown in-page without navigation"
  - "Web3Forms access_key left as placeholder 'YOUR_ACCESS_KEY_HERE' — requires user_setup before form sends emails"
  - "hCaptcha activated by both: (a) h-captcha div in HTML and (b) enabling Spam Protection in Web3Forms dashboard — HTML alone is insufficient"
  - "Term program data (Term 1 2026) uses realistic Scouts QLD activities, not lorem ipsum — group leader should verify exact dates"

patterns-established:
  - "Page hero pattern: bg-scout-maroon text-white py-16 md:py-20 with centered font-display font-black heading"
  - "Two-column layout: grid grid-cols-1 md:grid-cols-2 gap-12 for form + info panels"
  - "Table + card dual-render: hidden md:block table for desktop, md:hidden card list for mobile"

requirements-completed: [ABUT-01, EVNT-01, CONT-01, CONT-02, CONT-03]

# Metrics
duration: 2min
completed: 2026-03-05
---

# Phase 2 Plan 03: About, Events, and Contact/Join Pages Summary

**Three content pages built with brand tokens: About (group background + values), Events (CMS-ready termProgram array), and Join (Web3Forms + hCaptcha form with fetch-based submission)**

## Performance

- **Duration:** 2 min
- **Started:** 2026-03-05T11:33:47Z
- **Completed:** 2026-03-05T11:35:57Z
- **Tasks:** 2 of 2
- **Files modified:** 3 (all created)

## Accomplishments

- Created About page at /about with four sections: hero, Our Story prose (group background + Scouting description), What is Scouting explanation with sections link, 3-value grid (Adventure/Community/Growth), and scout-gold CTA linking to /join (ABUT-01)
- Created Events page at /events with CMS-compatible `termProgram` array (Term 1 2026, 5 realistic activity entries), responsive dual-render (desktop table / mobile card list), mailing-list callout, and join CTA (EVNT-01)
- Created Join page at /join with two-column layout — Web3Forms form with all required fields (name, email, child_age, message), hCaptcha widget, honeypot field, client-side fetch submission handler, and section age-range info panel with "What happens next?" onboarding steps (CONT-01, CONT-02, CONT-03)
- All pages use BaseLayout with scout-maroon heroes and consistent brand tokens from Plan 01

## Task Commits

Each task was committed atomically:

1. **Task 1: Create About page and Events/Term Program page** - `5ffde7a` (feat)
2. **Task 2: Create Contact/Join page with Web3Forms and hCaptcha** - `429caf7` (feat)

**Plan metadata:** (docs commit — see state updates below)

## Files Created/Modified

- `src/pages/about.astro` — Hero, Our Story (group + Scouting background), What is Scouting, 3-value grid, gold CTA
- `src/pages/events.astro` — CMS-compatible termProgram array, responsive table/card layout, mailing list callout
- `src/pages/join.astro` — Web3Forms form, hCaptcha, section info cards, "What happens next?" onboarding steps

## Decisions Made

- `termProgram` entries use `sections` (string) instead of `section` (enum) so "All sections" and "Scouts, Venturers" are both valid values — this maps directly to a CMS text field in Phase 4 without schema changes.
- Form uses `e.preventDefault()` + `fetch()` instead of native form POST so the success/error message can be shown inline without navigating away from the page.
- The Web3Forms `access_key` is left as `YOUR_ACCESS_KEY_HERE` per plan design — this is intentional. The form will not send emails until the group leader completes user_setup (register at web3forms.com, get key, replace placeholder, enable hCaptcha in dashboard).

## Deviations from Plan

None — plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

The contact form will NOT forward emails until the following two steps are completed:

**Step 1 — Get Web3Forms access key:**
1. Visit https://web3forms.com/#start
2. Enter the group's email address
3. Receive access key immediately via email
4. Replace `YOUR_ACCESS_KEY_HERE` in `src/pages/join.astro` line 21 with the real key

**Step 2 — Enable hCaptcha in Web3Forms dashboard:**
1. Visit https://app.web3forms.com
2. Select the Cooroy Scouts form
3. Go to Spam Protection > Enable hCaptcha
4. Save settings

Without Step 2, the hCaptcha widget will not appear even though the HTML div is present.

**Verification:** Submit the form with a real email — confirm email arrives at the group inbox.

## Next Phase Readiness

- All three non-section content pages are live: /about, /events, /join
- Nav links to /about, /events, /gallery, /join — /gallery will 404 until Phase 3
- The termProgram data structure in events.astro is already compatible with the Phase 4 CMS content collection schema — no migration work needed beyond connecting the CMS collection
- Web3Forms integration is fully wired; only the access key placeholder needs updating before go-live
- Phase 3 (Gallery) can now proceed — the site navigation is complete except for /gallery

## Self-Check: PASSED

Files confirmed present:
- src/pages/about.astro — FOUND
- src/pages/events.astro — FOUND
- src/pages/join.astro — FOUND

Commits verified:
- 5ffde7a — FOUND (Task 1: About + Events pages)
- 429caf7 — FOUND (Task 2: Join page)

---
*Phase: 02-content-pages*
*Completed: 2026-03-05*
