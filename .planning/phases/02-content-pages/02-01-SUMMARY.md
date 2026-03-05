---
phase: 02-content-pages
plan: "01"
subsystem: ui
tags: [astro, tailwind, brand, design-tokens, scouts-qld, homepage]

# Dependency graph
requires:
  - phase: 01-foundation
    provides: Astro 5 + Tailwind v4 project structure, BaseLayout, Nav, Footer, global.css @theme tokens
provides:
  - Official Scouts QLD brand tokens in global.css @theme (maroon, navy, gold, 4 section colors, Nunito Sans)
  - Migrated Nav component with scout-maroon header and official Scouts QLD white logo
  - Migrated Footer component with scout-navy background
  - Updated favicon with #681d34 QLD Maroon
  - Homepage with scout-maroon hero, section-specific color cards, and individual section page links
affects:
  - 02-content-pages/02-02 (sections pages consume section color tokens)
  - 02-content-pages/02-03 (all new pages use Nav/Footer with updated brand)
  - All future phases (brand token foundation is now correct)

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "@theme CSS custom properties as Tailwind utilities (bg-scout-maroon, bg-section-joeys, etc.)"
    - "Section-specific color tokens per Scouts Australia Brand Book page 24"
    - "Nunito Sans as single font for display and body (Brand Book standard)"

key-files:
  created: []
  modified:
    - src/styles/global.css
    - src/components/Nav.astro
    - src/components/Footer.astro
    - src/pages/index.astro
    - public/favicon.svg

key-decisions:
  - "scout-maroon (#681d34) is the Tailwind primary brand token replacing scout-green (#077840)"
  - "scout-navy (#28265c) used for Footer background and CTA text on gold buttons"
  - "Nunito Sans replaces Montserrat+Nunito as per Scouts Australia Brand Book"
  - "Official scouts-qld-white.png logo used in Nav (was text-only wordmark)"
  - "Section colors defined as section-joeys/cubs/scouts/venturers (not scout-[name])"

patterns-established:
  - "Brand token pattern: --color-scout-[name] for brand colors, --color-section-[name] for section colors"
  - "Gold (#F0CB37) reserved for CTA buttons and accent highlights; never used as primary background"
  - "Section cards: bg-section-[name] header + white card body + maroon 'Learn more' link"

requirements-completed: [HOME-01, HOME-02]

# Metrics
duration: 3min
completed: 2026-03-05
---

# Phase 2 Plan 01: Brand Token Migration and Homepage Summary

**QLD Maroon (#681d34) replaces scout-green as primary brand colour across all Tailwind @theme tokens, Nav, Footer, and homepage; Nunito Sans set as single typeface; 4 official section colour tokens added; homepage section cards link to individual /sections/[name] pages**

## Performance

- **Duration:** 3 min
- **Started:** 2026-03-05T11:28:21Z
- **Completed:** 2026-03-05T11:31:03Z
- **Tasks:** 3 of 3
- **Files modified:** 5

## Accomplishments

- Replaced all scout-green (#077840) design tokens with official Scouts QLD brand colours: scout-maroon (#681d34) as primary, scout-navy (#28265c) as secondary
- Added 4 section-specific color tokens (joeys brown, cubs yellow, scouts green, venturers maroon) per Scouts Australia Brand Book page 24
- Migrated Nav header to scout-maroon, replaced text wordmark with official scouts-qld-white.png logo, updated CTA text to text-scout-navy on gold background
- Migrated Footer background to scout-navy; all gold accents preserved throughout
- Updated homepage hero to scout-maroon, section cards to section-specific tokens, all card "Learn more" links now point to individual /sections/[name] pages (HOME-02)
- Switched font from Montserrat+Nunito to Nunito Sans (Scouts Australia Brand Book standard)
- Updated favicon from green (#077840) to QLD Maroon (#681d34)
- Zero scout-green references remain in src/ — verified by grep

## Task Commits

Each task was committed atomically:

1. **Task 1: Update brand design tokens in global.css** - `238631a` (chore)
2. **Task 2: Migrate Nav, Footer, and favicon to new color palette** - `c5e2ab6` (feat)
3. **Task 3: Update homepage with real content, section colors, and individual section links** - `c5cb25a` (feat)

**Plan metadata:** (docs commit — see state updates below)

## Files Created/Modified

- `src/styles/global.css` - Replaced @theme block: scout-maroon, scout-navy, scout-gold, 4 section colors, additional brand colors, Nunito Sans; updated Google Fonts import
- `src/components/Nav.astro` - Maroon header, official Scouts QLD white PNG logo, navy CTA text, maroon-dark mobile menu
- `src/components/Footer.astro` - Navy background replacing green; all gold accents retained
- `src/pages/index.astro` - Maroon hero, section-specific card colors, individual section links, navy headings and CTA
- `public/favicon.svg` - Fill updated from #077840 to #681d34

## Decisions Made

- Official scouts-qld-white.png logo used in Nav — Phase 1 used a text-only wordmark pending Scouts QLD permission; the Logos/ folder in the repo root confirmed the assets are available.
- Scout-gold (#F0CB37) retained as-is for all CTA buttons and accent highlights — this colour appears consistent across official Scouts Australia brand materials.
- Section color tokens use the prefix `section-` not `scout-` to distinguish them from primary brand colours (e.g., `bg-section-joeys` not `bg-scout-joeys`).

## Deviations from Plan

None — plan executed exactly as written.

## Issues Encountered

None.

## User Setup Required

None — no external service configuration required.

## Next Phase Readiness

- All brand tokens are now correct; Plans 02-02 and 02-03 can build section pages and additional pages using `bg-section-[name]` tokens
- The four /sections/[name] pages (joeys, cubs, scouts, venturers) are linked from the homepage but not yet created — these are the deliverable of Plan 02-02
- Nav links to /about, /events, /gallery — these pages don't exist yet; Astro will generate 404s for those routes until later plans build them

## Self-Check: PASSED

All created/modified files confirmed present. All 3 task commits verified in git log (238631a, c5e2ab6, c5cb25a).

---
*Phase: 02-content-pages*
*Completed: 2026-03-05*
