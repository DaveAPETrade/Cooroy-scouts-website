# Phase 1: Foundation - Context

**Gathered:** 2026-03-05
**Status:** Ready for planning

<domain>
## Phase Boundary

Astro project scaffold with Tailwind CSS v4, Scouts-branded design system, responsive BaseLayout (Nav + Footer), and live Cloudflare Pages deployment. This phase delivers the shell that every subsequent page inherits. No content pages are built here — just the layout, branding, and deployment pipeline.

</domain>

<decisions>
## Implementation Decisions

### Color scheme
- Primary color: Bright scout green — vibrant, energetic, close to the Scouts Australia fleur-de-lis green
- Accent color: Gold/yellow — classic scout pairing, high contrast for CTAs and highlights
- Must comply with Scouts QLD brand guidelines (use official hex values, not eyedropper samples)
- Source official brand assets (logo, colors, fonts) from Scouts QLD brand kit before committing design tokens

### Navigation layout
- Main nav items: Home, Sections, About, Events, Gallery + "Join Us" as a standout CTA button
- Sections and mobile nav organization: Claude's discretion
- "Join Us" / "Contact" must be a visually distinct highlighted button in the nav bar — primary conversion action always visible
- Footer includes: contact details (email, phone if applicable), links to official Scouts QLD site and registration form, social media (Facebook page link)
- Additional footer content at Claude's discretion

### Visual personality
- Active and outdoorsy vibe — big outdoor photos, adventure feel, emphasis on what kids DO (camping, hiking, building)
- Typography: Claude's discretion — pick fonts that match the active/outdoorsy palette
- Placeholder imagery: Scout stock photos of outdoor/scouting activities until real group photos are available
- UI element style (rounded vs sharp corners): Claude's discretion

### Page structure
- Hero style: Claude's discretion (full-width vs contained — lean toward what supports the active/outdoorsy vibe)
- Content max-width: Claude's discretion
- Column layout: Claude's discretion
- Whitespace / density: Claude's discretion

### Claude's Discretion
- Typography choice (font families, sizes, weights)
- Exact green and gold hex values (within Scouts QLD brand compliance)
- Navigation dropdown vs individual links for sections
- Mobile nav pattern (hamburger vs other)
- Hero section approach (full-width photo vs contained)
- Content max-width
- Single column vs sidebar on content pages
- Spacing and density
- UI corner radius
- Footer layout and additional content

</decisions>

<specifics>
## Specific Ideas

- "Same stack as the APE website" — developer has prior Astro + Tailwind + Cloudflare Pages experience
- "Design should be its own thing — not a copy of noosaseascouts.com.au but similar scope"
- Green + gold is a deliberate choice to lean into scouting's global identity rather than QLD's maroon/navy
- The site should feel like an active scout group — parents should see adventure, not a corporate org
- Real content exists in `new text document.md` at project root — section descriptions, join page copy, homepage welcome text. This content is for Phase 2 but informs the design system's content context (paragraph lengths, heading hierarchy, CTA patterns)
- Age ranges in provided content differ from initial discussion — use document's ages as working version: Joeys 5-8, Cubs 8-11, Scouts 11-15, Venturers 15-18 (confirm with group)

</specifics>

<code_context>
## Existing Code Insights

### Reusable Assets
- None — greenfield project, no existing code

### Established Patterns
- Developer experienced with Astro + Tailwind + Cloudflare Pages from APE website project
- Tailwind v4 requires `@tailwindcss/vite` plugin (NOT deprecated `@astrojs/tailwind`)

### Integration Points
- GitHub remote: https://github.com/DaveAPETrade/Cooroy-scouts-website.git
- Cloudflare Pages deployment needs to be connected to this repo
- Cloudflare Auto Minify for HTML must be disabled after first deploy (breaks Astro hydration)

</code_context>

<deferred>
## Deferred Ideas

- Badge/resources section mentioned in content document — could be a useful page but not in current v1 scope. Note for roadmap backlog.
- Fee transparency with real dollar amounts — deferred to v2 (SECT-07)

</deferred>

---

*Phase: 01-foundation*
*Context gathered: 2026-03-05*
