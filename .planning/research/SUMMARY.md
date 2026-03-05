# Project Research Summary

**Project:** Cooroy Scout Group Website
**Domain:** Static informational website with git-based CMS (volunteer-maintained, community organisation)
**Researched:** 2026-03-05
**Confidence:** HIGH

## Executive Summary

The Cooroy Scout Group website is a static informational site whose primary job is converting interested parents into member families. Research across 6+ Australian scout group sites confirms a consistent pattern: parents arrive by phone/search with a specific child age in mind, need to immediately locate their section, see meeting times, and find a low-friction path to make contact. The recommended approach is Astro 5.x (static build, island architecture) + Tailwind CSS 4.x + Sveltia CMS (git-based, browser-based editor) deployed to Cloudflare Pages. This stack is zero-cost on free tiers, zero-ops once deployed, and keeps volunteer editors in a familiar browser interface without GitHub knowledge.

The distinguishing characteristic of this build is the CMS requirement. Term programs change 4x per year and must be editable by non-technical volunteers without developer involvement. The entire architecture — content collections, dynamic routes, git-based CMS — flows from this single constraint. Sveltia CMS is recommended over Decap CMS (unmaintained) and TinaCMS (requires a backend server incompatible with static Cloudflare Pages). Sveltia is a drop-in Decap replacement with a 5x smaller bundle and first-party Cloudflare Workers auth, making setup straightforward.

The critical risks are not technical: placeholder content shipping to production is the most likely failure mode for this project. A group leader who is unavailable at launch leaves the site with "TBC" meeting times and no events, which signals to prospective parents that the group is inactive. The second risk is CMS OAuth on Cloudflare Pages — this requires a Cloudflare Worker auth proxy and is non-obvious from the Astro docs. Both risks have clear prevention strategies and must be treated as launch gates, not afterthoughts.

## Key Findings

### Recommended Stack

The stack is a well-validated combination for exactly this use case. Astro 5.x provides file-based routing, content collections with Zod validation, and zero-JS output by default. Tailwind CSS 4.x (using the new `@tailwindcss/vite` plugin, not the deprecated `@astrojs/tailwind` integration) provides responsive design with minimal effort. Cloudflare Pages provides free global CDN hosting with automatic GitHub-triggered rebuilds. The entire stack operates within free tiers given the low traffic volume of a community scout group site.

Sveltia CMS is the CMS recommendation, not Decap CMS. Decap is unmaintained (Netlify dropped support) and known for a bloated 1.5 MB bundle and slow REST API. Sveltia is its drop-in replacement using identical YAML config files, 300 KB bundle, and has a first-party `sveltia-cms-auth` Cloudflare Worker for OAuth — the step that causes the most friction in Decap deployments.

**Core technologies:**
- Astro 5.18.x: Static site framework — island architecture, content collections, zero-JS default, type-safe markdown
- Tailwind CSS 4.2.x: Utility-first CSS — v4 uses `@tailwindcss/vite` plugin (not deprecated `@astrojs/tailwind`)
- Sveltia CMS 0.x: Git-based CMS — Decap-compatible YAML config, 300 KB bundle, first-party Cloudflare Workers auth
- Cloudflare Pages: Hosting and CI/CD — free tier, global CDN, auto-deploy on GitHub push
- Web3Forms: Contact form email forwarding — 250 submissions/month free, no backend, built-in hCaptcha
- Astro `<Image>` / `<Picture>`: Image optimisation — WebP/AVIF output, responsive srcset, CLS prevention

### Expected Features

Based on analysis of 4+ Australian scout group sites, the feature landscape is well-defined. Parents need immediate answers to: "What age is this for?", "When and where do they meet?", and "How do I sign up?". Every feature decision should serve these three questions.

**Must have (table stakes):**
- Homepage hero with "Join Us" CTA — first thing a prospective parent sees; CTA must be above the fold
- Four section pages (Joeys, Cubs, Scouts, Venturers) — one per age group with meeting day/time/location
- Contact / Join Us page with form — conversion path; must not require raw email or phone as the only option
- About page — establishes trust; real leader names and group history reduce parent anxiety
- Term program / events page — CMS-managed; demonstrates the group is active; updated 4x/year
- Photo gallery — parents use this to judge whether the group looks active and welcoming
- Responsive design using Scouts QLD branding — non-negotiable; over 50% of visits are mobile
- SSL/HTTPS — Cloudflare Pages provides this automatically; no effort required

**Should have (competitive advantage for Cooroy specifically):**
- "First meeting is free" CTA copy — eliminates the commitment barrier that stops parents from reaching out
- Fee transparency on section/contact pages — reduces post-inquiry sticker shock; most QLD sites still hide fees
- Age progression pathway overview — parents with a 4yo wonder what happens in a year; showing Joeys → Venturers builds long-term commitment
- Cooroy-specific identity layer over QLD branding — local photos, hinterland references; differentiates from generic templates
- Volunteer recruitment call-out — leaders are the scarcest resource; surface this on the About or homepage

**Defer (v2+):**
- CMS expansion beyond term program page — v1 only requires the events page CMS-managed; expand when volunteers demonstrate need
- Social media feed embed — only useful if the Facebook page is consistently active; stale feed is worse than none
- Achievements / badges showcase — requires sustained content input from leaders; prove content supply first

**Anti-features to explicitly avoid:**
- Member login / password-protected areas — breaks the static model; use the Scouts Australia member portal instead
- Online payments / registration — links to the official Scouts QLD membership form instead
- Real-time RSVP calendar — double-entry burden for volunteers; a term program page updated 4x/year is sufficient
- Full children's names in photo captions — digital safeguarding risk per Scouts Australia guidance
- News / blog — volunteer groups don't maintain blogs; stale posts actively harm trust

### Architecture Approach

The architecture follows a four-layer pattern: Editor Layer (Sveltia CMS browser UI) → Source Layer (markdown files in `src/content/`, Astro pages and components) → Build Layer (Astro static build processing markdown into HTML, optimising images to WebP/AVIF) → Delivery Layer (Cloudflare Pages CDN + Web3Forms for contact). Volunteers never touch code or GitHub directly — they use the `/admin` CMS UI which commits markdown files to the repo, triggering an automatic Cloudflare rebuild (live within ~60 seconds).

**Major components:**
1. `BaseLayout.astro` — site shell containing `<html>`, `<head>`, Nav, and Footer; required by every page; must be built first
2. `src/content/` + `content.config.ts` — markdown files edited by CMS, Zod schemas providing type-safe validation
3. `src/pages/sections/[slug].astro` — dynamic route generating one page per scout section from the content collection
4. `src/pages/events.astro` — term program page querying the events collection; the primary CMS-managed page
5. `ContactForm.astro` — Web3Forms-backed contact form with honeypot spam protection
6. `GalleryGrid.astro` — photo grid using Astro `<Image>` for WebP optimisation; lightbox via astro-pandabox
7. `public/admin/` — Sveltia CMS config (`config.yml`) + entry HTML; mirrors `content.config.ts` schema
8. Sveltia CMS Auth Worker — Cloudflare Worker handling GitHub OAuth; deployed separately before CMS go-live

**Build order implication from architecture:** BaseLayout → Nav/Footer → content.config.ts → static pages → dynamic pages → gallery → CMS config → OAuth worker. The CMS integration is last because it requires a finalised content schema and a live deployment URL for the OAuth callback.

### Critical Pitfalls

1. **CMS OAuth silently fails on Cloudflare Pages** — Sveltia/Decap CMS docs default to Netlify's built-in auth. On Cloudflare Pages, a Cloudflare Worker OAuth proxy must be deployed and a GitHub OAuth App configured before any CMS editing is possible. Use the first-party `sveltia-cms-auth` Worker (30-second deploy). Verify end-to-end: login → edit → save → confirm git commit exists. Address in the CMS setup phase, before handing to volunteers.

2. **Images placed in `public/` bypass all optimisation** — Astro's `<Image>` component only processes files in `src/`. Gallery images added to `public/` are served as full-resolution JPEGs. A gallery with 20 unoptimised 4 MB phone photos will be unusable on mobile. Set the CMS `media_folder` to `src/images/gallery` from day one. Never change this pattern after images are added — recovery means re-uploading every image.

3. **Placeholder content ships to production and persists** — The group leader being unavailable at launch does not make placeholder content acceptable. Missing real meeting times signals an inactive group to prospective parents, directly destroying the site's core purpose. Treat content completeness (real meeting times, real contact email, at least one term program entry) as a launch gate. Use the old site from the Wayback Machine as a content source for initial fill.

4. **Tailwind v4 integration via deprecated `@astrojs/tailwind`** — The `@astrojs/tailwind` integration is deprecated as of Astro 5.2 and only supports Tailwind v3. Using it creates a subtle mismatch: `tailwind.config.js` syntax, v3 utility classes, and missing v4 features. Use `@tailwindcss/vite` directly in `astro.config.mjs` and configure with `@theme {}` in global CSS. This cannot be retrofitted easily once components are built.

5. **Cloudflare Auto Minify breaks Astro hydration** — Cloudflare's edge HTML minification corrupts Astro's hydration markers for any JavaScript islands (lightbox, form AJAX). Disable Auto Minify for HTML in Cloudflare Speed settings immediately after first deployment. Astro minifies at build time; edge minification is redundant and harmful.

6. **Scouts QLD brand assets sourced from Wayback Machine** — Scouts Australia has formal brand usage guidelines. Logo files and hex colour codes must come from official Scouts QLD documentation, not screenshots or archived pages. Get written confirmation of approved assets before committing design tokens to the Tailwind config.

## Implications for Roadmap

Based on the combined research, the architecture's build order, feature dependencies, and pitfall prevention phases map directly to a natural phase structure. Phases must respect the dependency chain: BaseLayout before pages, content schema before CMS config, live deployment URL before OAuth setup.

### Phase 1: Foundation and Design System

**Rationale:** BaseLayout and design tokens are required by every subsequent page. Committing incorrect brand colours (sourced from unapproved assets) would require a global find-replace later. All other phases depend on this being correct and complete.
**Delivers:** Working Astro project with Cloudflare Pages deployment, Tailwind v4 configured, Scouts QLD brand tokens in `@theme {}`, BaseLayout with Nav/Footer, a custom 404 page, and confirmed SSL.
**Addresses:** Mobile-responsive design (table stakes), Scouts QLD branding (table stakes)
**Avoids:** Deprecated `@astrojs/tailwind` pitfall, brand asset sourcing pitfall, Cloudflare Auto Minify pitfall (configure on first deploy)
**Research flag:** Standard — Astro + Tailwind v4 + Cloudflare Pages setup is well-documented with official guides. No additional research needed.

### Phase 2: Static Content Pages

**Rationale:** Homepage, About, and Contact pages are static (no collection dependency). They can be built immediately after the design system. The Contact page must exist before the homepage CTA links to it.
**Delivers:** Homepage with hero and "Join Us" CTA, About page with group history and leader info, Contact/Join Us page with Web3Forms form (honeypot + hCaptcha spam protection configured), and a thank-you redirect page.
**Addresses:** Homepage hero (table stakes), About page (table stakes), Contact form (table stakes), "first meeting is free" CTA copy (differentiator), volunteer recruitment call-out (differentiator)
**Avoids:** Buried CTA pitfall, contact form spam pitfall, contact form with no confirmation message pitfall
**Research flag:** Standard — Web3Forms integration is straightforward HTML form with an access key. Spam protection configuration is documented.

### Phase 3: Section Pages

**Rationale:** Section pages are the highest-priority informational content for prospective parents. They require `content.config.ts` and markdown files to be in place before the dynamic `[slug].astro` route works. Building the Zod schema here sets the foundation for the CMS config in Phase 5.
**Delivers:** Four section pages (Joeys, Cubs, Scouts, Venturers) generated from content collections, each with real meeting day/time/location content (not placeholders), plus an age progression pathway overview.
**Addresses:** Section pages (table stakes), meeting time and location (table stakes), age progression pathway (differentiator), fee transparency content (differentiator)
**Avoids:** Placeholder content pitfall (real meeting data required before phase is complete), missing content that destroys conversion
**Research flag:** Standard — Astro content collections with `getStaticPaths()` is well-documented. The section content schema is simple.

### Phase 4: Gallery

**Rationale:** Gallery requires the image pipeline to be established before any photos are added. Setting the `src/images/` pattern (not `public/`) before images are committed prevents the costly recovery of re-uploading all photos later.
**Delivers:** Photo gallery page using Astro `<Image>` (WebP/AVIF output), astro-pandabox lightbox, all images in `src/images/gallery/`, descriptive alt text on every image, activity-focused captions (no full children's names).
**Addresses:** Photo gallery (table stakes), mobile gallery performance, digital safeguarding (no child name/location combos)
**Avoids:** `public/` image optimisation pitfall (primary risk for this phase), unoptimised gallery causing Lighthouse failure, accessibility failures (alt text)
**Research flag:** Standard — Astro `<Image>` + astro-pandabox is a documented combination. Image path configuration requires attention to `media_folder` setting.

### Phase 5: CMS Integration

**Rationale:** CMS setup is intentionally last among the core feature phases because it requires a finalised content schema (defined in Phase 3) and a live Cloudflare Pages deployment URL (available after Phase 1). Setting up OAuth before the deployment URL is known is impossible. Sveltia CMS config.yml must mirror content.config.ts exactly — schema changes after CMS setup cause field mismatches and broken builds.
**Delivers:** Sveltia CMS accessible at `/admin`, `sveltia-cms-auth` Cloudflare Worker deployed, GitHub OAuth App configured, `config.yml` matching content schema exactly, events collection CMS-editable, verified end-to-end (volunteer logs in → edits term program → git commit appears → Cloudflare rebuild completes).
**Addresses:** Term program / events page CMS-managed (table stakes), volunteer editor independence from developers
**Avoids:** CMS OAuth silent failure pitfall (must be verified end-to-end), schema divergence between content.config.ts and config.yml, CMS media_folder misconfiguration
**Research flag:** Needs deeper research. Sveltia CMS OAuth setup on Cloudflare Pages is less documented than Decap's equivalent. The `sveltia-cms-auth` Worker deploy process should be verified step-by-step before planning this phase.

### Phase 6: Content Population and Launch

**Rationale:** Content completeness is a launch gate, not an afterthought. This phase exists to prevent placeholder content from shipping. The group leader must be engaged before this phase begins.
**Delivers:** All section pages populated with real content from the group leader, term program page with at least one real term entry, gallery with real group photos (activity-focused), contact form email pointing to the group leader's inbox (not the developer's), accessibility audit passing (axe/Lighthouse >90), full launch checklist verified.
**Addresses:** All content-dependent table stakes features, fee transparency (confirm current fees with group), Cooroy-specific identity layer (real local photos)
**Avoids:** Placeholder content launch pitfall (the most likely failure mode for this project), contact form delivering to wrong inbox, missing alt text on real photos
**Research flag:** No research needed. This is a content and coordination phase.

### Phase Ordering Rationale

- Foundation first because every subsequent component inherits brand tokens and BaseLayout — wrong colours or layout structure cannot be corrected without touching every file.
- Static pages before dynamic pages because the homepage CTA requires the Contact page to exist, and About/Contact have no collection dependencies to wait for.
- Section pages before CMS because the Zod schema must be finalised before the CMS YAML config can mirror it; schema changes after CMS setup create double-maintenance.
- Gallery before CMS because the `media_folder` setting in `config.yml` must point to `src/images/gallery/` — establishing the correct path before any CMS upload prevents later image re-migration.
- CMS last among feature phases because it requires both a live deployment URL (for OAuth callback) and a finalised content schema (for `config.yml` accuracy).
- Content population as a distinct final phase because it requires group leader engagement and treats content completeness as a hard requirement, not a "fill it in later" assumption.

### Research Flags

Phases likely needing `/gsd:research-phase` during planning:
- **Phase 5 (CMS Integration):** Sveltia CMS on Cloudflare Pages is less documented than Decap equivalents. The `sveltia-cms-auth` Worker setup and GitHub OAuth App configuration should be researched step-by-step. Specifically: how `config.yml` `backend.base_url` is configured when using the auth Worker, and whether local development requires a companion server (as Decap requires `npx decap-server`).

Phases with standard patterns (skip research-phase):
- **Phase 1 (Foundation):** Astro 5.x + Tailwind v4 + Cloudflare Pages — official guides exist and are up to date.
- **Phase 2 (Static Pages):** Web3Forms HTML integration is a simple access-key pattern with official documentation.
- **Phase 3 (Section Pages):** Astro content collections + `getStaticPaths()` — official documentation is comprehensive and the pattern is stable.
- **Phase 4 (Gallery):** Astro `<Image>` + astro-pandabox — documented combination; main risk is configuration, not research.
- **Phase 6 (Content):** No research needed; coordination and content work only.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | Core technologies verified via official docs and npm. Sveltia CMS recommendation is MEDIUM — it is beta (0.x) and the auth Worker documentation is less mature than Decap's; production viability is confirmed by community use. |
| Features | HIGH | Pattern confirmed across 6+ Australian scout group sites. Feature priorities and anti-features are well-grounded in both competitor analysis and nonprofit UX research. |
| Architecture | HIGH | Based entirely on official Astro, Cloudflare Pages, and Formspree docs. Build order and component boundaries are well-established patterns for this stack. |
| Pitfalls | HIGH (technical) / MEDIUM (UX/content) | CMS OAuth, image pipeline, and Tailwind integration pitfalls are verified against official docs and confirmed community issues. UX and content pitfalls are pattern-based across multiple nonprofit sources. |

**Overall confidence:** HIGH

### Gaps to Address

- **Sveltia CMS auth Worker step-by-step:** The `sveltia-cms-auth` repo exists and is maintained but the documentation is thinner than Decap OAuth equivalents. During Phase 5 planning, research the exact Worker deploy sequence and local development story before writing tasks.
- **Scouts QLD official brand assets:** The hex colour values and approved logo files must be obtained directly from Scouts QLD before Phase 1 begins. If the group leader cannot provide these, contact the state body. Do not proceed with design tokens based on eyedropper sampling.
- **Real meeting times and location:** The section content (Phase 3) cannot be completed without this information from the group leader. If the leader is unavailable, source it from the old site via the Wayback Machine as a starting point, but confirm accuracy before launch.
- **Web3Forms vs. Web3Forms note in FEATURES.md:** FEATURES.md references Formspree in the feature dependencies section (a legacy reference from before CMS selection was finalised). The stack decision is Web3Forms. During Phase 2 planning, use Web3Forms exclusively — ignore Formspree references in FEATURES.md.
- **Astro v6:** Astro v6 is in beta as of March 2026. If it reaches stable during development, assess migration risk. For a content site this size, the upgrade is low-risk but should be a deliberate decision, not accidental.

## Sources

### Primary (HIGH confidence)
- https://docs.astro.build/en/guides/content-collections/ — Content collections, Zod schemas, getStaticPaths
- https://docs.astro.build/en/basics/project-structure/ — public/ vs src/ distinction
- https://docs.astro.build/en/guides/images/ — Image component, WebP/AVIF, CLS prevention
- https://docs.astro.build/en/guides/integrations-guide/tailwind/ — @astrojs/tailwind deprecation confirmed
- https://docs.astro.build/en/guides/cms/decap-cms/ — Decap CMS + Astro integration pattern
- https://tailwindcss.com/blog/tailwindcss-v4 — Tailwind v4 CSS-first config (@theme)
- https://developers.cloudflare.com/pages/framework-guides/deploy-an-astro-site/ — Cloudflare Pages build settings
- https://web3forms.com/pricing — Free tier limits confirmed (250 submissions/month)
- https://tina.io/pricing — TinaCMS free tier user cap confirmed (2 users)
- https://formspree.io/guides/astro/ — Formspree AJAX limitations on free plan

### Secondary (MEDIUM confidence)
- https://github.com/sveltia/sveltia-cms — Sveltia CMS GitHub: beta status, bundle size, Decap config compatibility
- https://github.com/sveltia/sveltia-cms-auth — Sveltia CMS Cloudflare Workers auth Worker
- https://decapcms.org/docs/external-oauth-clients/ — External OAuth clients for Decap on non-Netlify hosts
- https://github.com/i40west/netlify-cms-cloudflare-pages — Cloudflare Pages OAuth proxy reference
- Ocean Grove Scouts, Epping Scouts, Beenleigh Scouts, Bundoora Scouts — feature pattern analysis

### Tertiary (LOW confidence)
- https://staticmania.com/blog/top-git-based-cms — Git-based CMS comparison (single source, cross-checked)
- https://nemanjamitic.com/blog/2025-04-02-astro-react-gallery/ — Astro image gallery pattern (general approach confirmed elsewhere)

---
*Research completed: 2026-03-05*
*Ready for roadmap: yes*
