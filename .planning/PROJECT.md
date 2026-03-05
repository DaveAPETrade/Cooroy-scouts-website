# Cooroy Scout Group Website

## What This Is

A public-facing website for Cooroy Scout Group (Scouts Queensland) aimed at parents and families considering scouting for their kids, and existing members needing group info. Static site built with Astro + Tailwind CSS, hosted on Cloudflare Pages, with a simple CMS so non-technical volunteers can update content like term programs.

## Core Value

Parents can quickly find what Cooroy Scouts offers, which section suits their child's age, and how to join — without needing to track down a leader.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Home page with hero, brief intro, and calls to action (join, learn more)
- [ ] About/History page with group background and scouting info
- [ ] Section pages (Joeys, Cubs, Scouts, Venturers) with age ranges, meeting times/location, and brief descriptions
- [ ] Events/Term Program page showing current term activities, updated ~4x/year via CMS
- [ ] Contact/Join Us page with form (email forwarding via Formspree or similar)
- [ ] Photo gallery page
- [ ] Simple CMS integration (Tina or Decap CMS) for volunteer content editing
- [ ] Responsive design based on Scouts Queensland branding with unique Cooroy identity
- [ ] Hosted on Cloudflare Pages with CI/CD from git

### Out of Scope

- Member login / authentication — not needed for an informational site
- E-commerce / payments — no online payments
- Real-time chat or messaging — static site, no backend
- Mobile app — web only
- Blog / news feed — events page covers updates
- Custom domain setup — scout group handles DNS

## Context

- Same stack as the APE website (developer has prior Astro + Tailwind + Cloudflare Pages experience)
- Content is placeholder initially — real content will come from the group leader (currently away) and the developer's wife who is involved with the group
- Old website exists on the Wayback Machine as a content reference
- Site scope inspired by similar scout group sites (e.g., noosaseascouts.com.au) but with its own design identity
- Scouts Queensland provides standard branding (logo, colors) as a foundation
- Four sections cover ages 5-17: Joeys (5-7), Cubs (8-10), Scouts (11-14), Venturers (15-17)

## Constraints

- **Tech stack**: Astro + Tailwind CSS — decided, matches existing experience
- **Hosting**: Cloudflare Pages — decided
- **CMS**: Must be git-based headless CMS (Tina or Decap) — non-technical editors need browser-based editing
- **Forms**: Email forwarding service (Formspree/Formspark) — no server-side processing
- **Content**: Placeholder content for v1, real content added later
- **Timeline**: No deadline — side project, ship when ready

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Astro + Tailwind CSS | Matches APE website stack, developer experience | — Pending |
| Cloudflare Pages hosting | Free tier, fast, familiar | — Pending |
| Git-based CMS (Tina/Decap) | Wife/volunteers need browser-based content editing | — Pending |
| Email forwarding for forms | No backend needed on static site | — Pending |
| Scouts QLD branding as base | Official group, use standard brand with Cooroy identity | — Pending |

---
*Last updated: 2026-03-05 after initialization*
