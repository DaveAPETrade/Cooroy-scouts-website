# Roadmap: Cooroy Scout Group Website

## Overview

Four phases take this from an empty repo to a live, volunteer-maintainable site. Foundation first — every page inherits the design system and BaseLayout, so brand tokens must be correct before any page is built. All public content pages build on that foundation as a single phase, grouping static and dynamic pages together because they share the same Astro project context. Gallery is separated from the other pages because it requires its own image pipeline setup (`src/images/` vs `public/`) — getting this wrong before any photos are added requires a costly re-migration. CMS integration is last because it requires both a live deployment URL (for the OAuth callback) and a finalised content schema — two things that only exist after Phases 1 and 2 are complete.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [ ] **Phase 1: Foundation** - Astro project, Tailwind v4 design system, Cloudflare Pages deployment, BaseLayout
- [ ] **Phase 2: Content Pages** - All public pages: homepage, about, sections, contact, events
- [ ] **Phase 3: Gallery** - Photo gallery with Astro image optimisation pipeline
- [ ] **Phase 4: CMS Integration** - Sveltia CMS at /admin with volunteer-accessible term program editing

## Phase Details

### Phase 1: Foundation
**Goal**: A live Astro site deployed to Cloudflare Pages with correct Scouts QLD branding, Tailwind v4 configured, and BaseLayout in place — so every subsequent page inherits an accurate design system
**Depends on**: Nothing (first phase)
**Requirements**: INFR-01, INFR-02, INFR-03
**Success Criteria** (what must be TRUE):
  1. The site URL on Cloudflare Pages loads a live page served over HTTPS
  2. A push to the GitHub main branch triggers an automatic Cloudflare Pages rebuild (CI/CD is active)
  3. The site renders correctly on mobile, tablet, and desktop (responsive BaseLayout with Nav and Footer)
  4. Scouts QLD brand colours and typography are applied from approved official assets, not eyedropper samples
**Plans:** 1/2 plans executed

Plans:
- [ ] 01-01-PLAN.md — Scaffold Astro project with Tailwind v4, brand design tokens, and responsive BaseLayout (Nav + Footer)
- [ ] 01-02-PLAN.md — Deploy to Cloudflare Pages with CI/CD from GitHub

### Phase 2: Content Pages
**Goal**: Every public-facing informational page exists with real content — parents can find the right section for their child's age, read about the group, and submit a contact inquiry
**Depends on**: Phase 1
**Requirements**: HOME-01, HOME-02, ABUT-01, CONT-01, CONT-02, CONT-03, SECT-01, SECT-02, SECT-03, SECT-04, SECT-05, SECT-06, EVNT-01
**Success Criteria** (what must be TRUE):
  1. Homepage hero is visible above the fold on mobile with a working "Join Us" CTA that links to the contact page
  2. A parent can navigate to any section page (Joeys, Cubs, Scouts, Venturers) and see the age range, real meeting day/time/location, and a description of what a typical night looks like
  3. The section pages include an overview showing the age progression pathway from Joeys through to Venturers
  4. A parent can submit the contact form with their name, child's age, and email — the submission arrives in the group's email inbox with spam protection active
  5. The events/term program page displays at least one real term entry (not placeholder content)
**Plans:** 3 plans

Plans:
- [ ] 02-01-PLAN.md — Update brand tokens (green to blue/navy) and migrate homepage with section-colored cards
- [ ] 02-02-PLAN.md — Create SectionProgression component and 4 individual section pages
- [ ] 02-03-PLAN.md — Create About, Events, and Contact/Join pages with Web3Forms

### Phase 3: Gallery
**Goal**: A photo gallery shows the group as active and welcoming, with all images served as optimised WebP — and the image pipeline is configured correctly from the start so no re-migration is needed later
**Depends on**: Phase 2
**Requirements**: GALL-01, GALL-02
**Success Criteria** (what must be TRUE):
  1. The gallery page displays group activity photos in a responsive grid that works on mobile
  2. All gallery images are served as WebP with responsive sizes (not full-resolution JPEGs)
  3. No photo caption includes a child's full name (digital safeguarding compliant)
**Plans**: TBD

### Phase 4: CMS Integration
**Goal**: A non-technical volunteer can log in to the CMS at /admin via their browser, edit the term program, save, and see the change live on the site within two minutes — without touching code or GitHub
**Depends on**: Phase 3
**Requirements**: EVNT-02, INFR-04
**Success Criteria** (what must be TRUE):
  1. A volunteer can navigate to /admin, authenticate via GitHub OAuth, and reach the Sveltia CMS editor
  2. The volunteer can edit the term program content, save it, and confirm a new git commit appears in the repository
  3. The Cloudflare Pages rebuild triggered by that commit completes and the updated term program is visible on the live site
**Plans**: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 → 2 → 3 → 4

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Foundation | 1/2 | In Progress|  |
| 2. Content Pages | 0/3 | Not started | - |
| 3. Gallery | 0/TBD | Not started | - |
| 4. CMS Integration | 0/TBD | Not started | - |
