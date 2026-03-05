# Phase 1: Foundation - Research

**Researched:** 2026-03-05
**Domain:** Astro 5 + Tailwind CSS v4 + Cloudflare Pages + Scouts QLD branding
**Confidence:** HIGH (stack) / MEDIUM (brand colors — requires official asset retrieval)

---

<user_constraints>
## User Constraints (from CONTEXT.md)

### Locked Decisions
- **Color scheme:** Primary = bright scout green (vibrant, close to Scouts Australia fleur-de-lis green). Accent = gold/yellow. Must use official Scouts QLD hex values — not eyedropper samples. Source from official brand kit before committing design tokens.
- **Navigation items:** Home, Sections, About, Events, Gallery + "Join Us" as a standout CTA button
- **"Join Us" / "Contact":** Must be a visually distinct highlighted button in nav bar — primary conversion action always visible
- **Footer:** Contact details (email, phone if applicable), links to official Scouts QLD site and registration form, social media (Facebook page link)
- **Visual personality:** Active and outdoorsy — big outdoor photos, adventure feel, emphasis on what kids DO
- **Placeholder imagery:** Scout stock photos of outdoor/scouting activities until real group photos available
- **Tailwind integration:** Use `@tailwindcss/vite` plugin — NOT deprecated `@astrojs/tailwind`
- **Deployment:** Cloudflare Pages connected to GitHub remote `https://github.com/DaveAPETrade/Cooroy-scouts-website.git`
- **Gallery images:** Must live in `src/images/gallery/` — NOT `public/` — so Astro Image can optimise them

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

### Deferred Ideas (OUT OF SCOPE)
- Badge/resources section — roadmap backlog only
- Fee transparency with real dollar amounts — deferred to v2 (SECT-07)
</user_constraints>

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| INFR-01 | Site is responsive across mobile, tablet, and desktop | BaseLayout with responsive Nav + Footer; Tailwind v4 mobile-first breakpoints (`sm:`, `md:`, `lg:`); hamburger nav pattern documented |
| INFR-02 | Site uses Scouts Queensland branding with unique Cooroy identity | Official Scouts Australia brand colors (green #077840, gold #F0CB37) verified; Tailwind v4 `@theme` tokens pattern documented; typography research complete |
| INFR-03 | Site deployed on Cloudflare Pages with CI/CD from GitHub | Cloudflare Pages GitHub-connected CI/CD documented; build settings verified; Node.js v22 default on new projects confirmed |
</phase_requirements>

---

## Summary

This phase scaffolds a greenfield Astro 5 project with Tailwind CSS v4, applies Scouts QLD brand tokens through Tailwind's `@theme` CSS directive, builds a responsive BaseLayout with Nav and Footer components, and deploys the live site to Cloudflare Pages wired to GitHub for automatic CI/CD deploys.

The developer has prior experience with this exact stack (referenced APE website). The key differences from a typical prior project are: (1) Tailwind v4 is CSS-first — all design tokens live in a `global.css` file using `@theme {}` blocks, not a `tailwind.config.js`; (2) The `@astrojs/tailwind` integration is deprecated and must NOT be used — use `@tailwindcss/vite` directly in `astro.config.mjs`; (3) Cloudflare Pages now uses Node.js v22 by default on new projects (v3 build image, May 2025), so no `NODE_VERSION` env var is needed for a fresh project.

The one area requiring human action before design tokens can be finalised is sourcing official Scouts QLD brand hex values. Research found strong community-consensus colors (#077840 green, #F0CB37 gold) that match the Scouts Australia brand book, but the current July 2025 PDF could not be parsed. These values should be treated as HIGH confidence starting points, with the official ScoutsQLD brand team as the authority for final confirmation.

**Primary recommendation:** Scaffold with `npm create astro@latest`, add `@tailwindcss/vite` directly to `astro.config.mjs`, define brand tokens in `@theme {}` inside `src/styles/global.css`, build Nav + Footer as standalone Astro components imported into `BaseLayout.astro`, then connect Cloudflare Pages to GitHub for CI/CD.

---

## Standard Stack

### Core

| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| astro | 5.x (latest) | Static site framework, file-based routing, layouts | Project decision; developer-familiar; zero-JS by default |
| tailwindcss | 4.x (latest) | Utility-first CSS, design tokens via `@theme` | Project decision; v4 is CSS-first, no config file needed |
| @tailwindcss/vite | 4.x (latest) | Vite plugin that wires Tailwind v4 into Astro | The correct v4 integration method — replaces deprecated `@astrojs/tailwind` |

### Supporting

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| @astrojs/check | latest | TypeScript type-checking for `.astro` files | Recommended for type-safe props in layouts/components |
| typescript | ~5.x | Type safety in Astro frontmatter and components | Enabled by default in Astro 5 minimal template |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| `@tailwindcss/vite` | `@astrojs/tailwind` | `@astrojs/tailwind` is deprecated for Tailwind v4; only supports v3 |
| Inline `<script>` for mobile nav | Alpine.js | Alpine is heavier; inline `<script>` or the checkbox hack works for a simple hamburger |
| Google Fonts via `<link>` | Self-hosted fonts | Google Fonts via `@import` in CSS is simpler; self-hosting is better for privacy/perf but adds complexity outside Phase 1 scope |

**Installation:**
```bash
npm create astro@latest cooroy-scouts-website -- --template minimal
cd cooroy-scouts-website
npm install tailwindcss @tailwindcss/vite
```

---

## Architecture Patterns

### Recommended Project Structure

```
cooroy-scouts-website/
├── src/
│   ├── components/
│   │   ├── Nav.astro            # Responsive navigation with mobile hamburger
│   │   └── Footer.astro         # Footer with contact, links, social
│   ├── layouts/
│   │   └── BaseLayout.astro     # Root HTML shell — imports global.css, slots content
│   ├── pages/
│   │   └── index.astro          # Landing page (placeholder for Phase 2)
│   ├── styles/
│   │   └── global.css           # @import "tailwindcss"; + @theme { ... brand tokens }
│   └── images/
│       └── gallery/             # Gallery images optimised by Astro Image (Phase 3)
├── public/
│   └── favicon.svg              # Static favicon, served as-is
├── astro.config.mjs             # Tailwind vite plugin registered here
├── tsconfig.json
└── package.json
```

### Pattern 1: BaseLayout with Typed Props

**What:** A single `.astro` layout that wraps every page. Imports `global.css` once, renders `<Nav>` and `<Footer>`, injects page content via `<slot />`.

**When to use:** Every page in the site inherits this layout, ensuring Nav and Footer are always present.

**Example:**
```astro
---
// src/layouts/BaseLayout.astro
// Source: https://docs.astro.build/en/basics/layouts/
import Nav from '../components/Nav.astro';
import Footer from '../components/Footer.astro';
import '../styles/global.css';

interface Props {
  title: string;
  description?: string;
}

const { title, description = 'Cooroy Scout Group — Scouts Queensland' } = Astro.props;
---
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="description" content={description} />
    <title>{title} | Cooroy Scout Group</title>
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
  </head>
  <body class="bg-white text-gray-900 font-body">
    <Nav />
    <main>
      <slot />
    </main>
    <Footer />
  </body>
</html>
```

### Pattern 2: Tailwind v4 CSS-First Design Tokens

**What:** All brand colours, fonts, and spacing are declared in `@theme {}` blocks inside `global.css`. No JavaScript config file.

**When to use:** Always — this is the Tailwind v4 approach. Tokens defined here become utility classes (`bg-scout-green`, `text-scout-gold`, `font-display`) and CSS variables (`var(--color-scout-green)`).

**Example:**
```css
/* src/styles/global.css */
/* Source: https://tailwindcss.com/docs/theme */

/* Google Fonts must be @imported BEFORE @import "tailwindcss" */
@import url("https://fonts.googleapis.com/css2?family=Nunito:wght@400;600;700;800&family=Montserrat:wght@700;800;900&display=swap");

@import "tailwindcss";

@theme {
  /* Brand Colors — verify hex values against official Scouts QLD brand kit */
  --color-scout-green:   #077840;   /* Scouts Australia primary green (community consensus; verify with official kit) */
  --color-scout-gold:    #F0CB37;   /* Scouts Australia gold (community consensus; verify with official kit) */
  --color-scout-green-dark:  #055c30;  /* Darker shade for hover states */
  --color-scout-gold-dark:   #d4a800; /* Darker gold for hover states */

  /* Typography */
  --font-display: "Montserrat", "Arial Black", sans-serif;  /* Headings — bold, outdoorsy */
  --font-body:    "Nunito", "Arial", sans-serif;             /* Body — friendly, readable */

  /* Custom breakpoints (Tailwind defaults sm/md/lg/xl are fine; add if needed) */
}
```

### Pattern 3: Responsive Navigation with Inline Script

**What:** Nav component that shows a horizontal nav on desktop and a hamburger-triggered slide-down menu on mobile. Uses a minimal inline `<script>` to toggle a CSS class — no framework dependency.

**When to use:** This approach keeps the nav as a pure Astro component (no island/hydration needed) while handling the one interactive requirement (menu toggle).

**Example:**
```astro
---
// src/components/Nav.astro
const navLinks = [
  { href: '/', label: 'Home' },
  { href: '/sections', label: 'Sections' },
  { href: '/about', label: 'About' },
  { href: '/events', label: 'Events' },
  { href: '/gallery', label: 'Gallery' },
];
---
<header class="bg-scout-green text-white sticky top-0 z-50 shadow-md">
  <nav class="max-w-6xl mx-auto px-4 py-3 flex items-center justify-between">
    <!-- Logo / wordmark -->
    <a href="/" class="font-display font-black text-xl text-white tracking-tight">
      Cooroy Scouts
    </a>

    <!-- Desktop nav links -->
    <ul class="hidden md:flex items-center gap-6 font-semibold text-sm">
      {navLinks.map(link => (
        <li>
          <a href={link.href} class="hover:text-scout-gold transition-colors">{link.label}</a>
        </li>
      ))}
      <li>
        <a href="/join" class="bg-scout-gold text-scout-green-dark font-bold px-4 py-2 rounded-full hover:bg-scout-gold-dark transition-colors">
          Join Us
        </a>
      </li>
    </ul>

    <!-- Mobile hamburger button -->
    <button id="menu-toggle" aria-label="Toggle navigation menu" class="md:hidden p-2">
      <span class="block w-6 h-0.5 bg-white mb-1.5"></span>
      <span class="block w-6 h-0.5 bg-white mb-1.5"></span>
      <span class="block w-6 h-0.5 bg-white"></span>
    </button>
  </nav>

  <!-- Mobile drawer -->
  <div id="mobile-menu" class="md:hidden hidden bg-scout-green-dark px-4 pb-4">
    <ul class="flex flex-col gap-3 pt-2 font-semibold text-sm">
      {navLinks.map(link => (
        <li>
          <a href={link.href} class="block py-2 hover:text-scout-gold">{link.label}</a>
        </li>
      ))}
      <li>
        <a href="/join" class="block bg-scout-gold text-scout-green-dark font-bold px-4 py-2 rounded-full text-center mt-2">
          Join Us
        </a>
      </li>
    </ul>
  </div>
</header>

<script>
  const toggle = document.getElementById('menu-toggle');
  const menu = document.getElementById('mobile-menu');
  toggle?.addEventListener('click', () => {
    menu?.classList.toggle('hidden');
  });
</script>
```

### Pattern 4: Cloudflare Pages Astro Build Config

**What:** `astro.config.mjs` wires in the Tailwind Vite plugin. No Cloudflare adapter needed for a static site.

**Example:**
```javascript
// astro.config.mjs
// Source: https://tailwindcss.com/docs/installation/framework-guides/astro
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
  },
});
```

### Anti-Patterns to Avoid

- **Using `@astrojs/tailwind`:** This integration is deprecated and does not support Tailwind v4. Using it causes v3 behaviour (requires `tailwind.config.js`) and breaks the CSS-first token approach.
- **Placing gallery images in `public/`:** Images in `public/` are served as-is; Astro's `<Image />` component cannot optimise them. Always use `src/images/` for any image that needs WebP conversion or responsive sizing.
- **Installing `@astrojs/cloudflare` adapter for a static site:** The Cloudflare adapter is only needed for on-demand/SSR rendering. A fully static Astro site deploys to Cloudflare Pages without any adapter.
- **Eyedroppering brand colours from screenshots:** The CONTEXT.md explicitly prohibits this. Use official hex values from the Scouts QLD brand kit PDF or from `scoutsqld.com.au`.
- **Defining CSS custom properties outside `@theme {}` for Tailwind tokens:** Custom properties defined with bare `:root { --color-x: ... }` do NOT become Tailwind utility classes in v4. They must be inside `@theme {}`.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| CSS utility classes | Custom utility class system | Tailwind v4 | 50,000+ tested utilities; purged at build; responsive variants; dark mode — impossible to replicate correctly |
| Image optimisation | `<img>` with manual WebP conversion | Astro `<Image />` component | Handles srcset, WebP, lazy loading, aspect ratio — complex to maintain manually |
| Mobile nav toggle | Custom CSS-only checkbox hack | Minimal inline `<script>` (4 lines) | Checkbox hack is inaccessible; inline script is trivial and doesn't require a framework |
| SEO meta tags | Per-page meta copy-paste | BaseLayout props + Astro `<head>` | Centralised in layout, typed Props interface catches missing values at build time |
| Responsive breakpoints | Custom media queries | Tailwind's `sm:`, `md:`, `lg:` variants | Consistent breakpoints across all components; mobile-first by convention |

**Key insight:** In an Astro + Tailwind project, virtually all visual and layout concerns are solved by existing utilities. The value-add is the brand layer (`@theme` tokens) and the component structure (BaseLayout, Nav, Footer).

---

## Common Pitfalls

### Pitfall 1: Using Deprecated `@astrojs/tailwind`

**What goes wrong:** Developer installs `@astrojs/tailwind` from prior experience. It runs but uses Tailwind v3 behaviour — `@import "tailwindcss"` does not work, tokens require `tailwind.config.js`, and the `@theme` directive is unknown.

**Why it happens:** `@astrojs/tailwind` is still published and appears in older tutorials.

**How to avoid:** Install `tailwindcss` + `@tailwindcss/vite`, add `tailwindcss()` to `vite.plugins` in `astro.config.mjs`. Do NOT add `tailwind()` to `integrations`.

**Warning signs:** Error "Unknown at-rule @theme" in CSS; no utility classes being generated from `global.css`.

### Pitfall 2: Brand Colours Committed Without Official Verification

**What goes wrong:** Design tokens are committed using eyedropper or community-consensus colours. Later, official Scouts QLD PDF confirms different values. All tokens, components, and screenshots must be redone.

**Why it happens:** Research found strong community consensus (#077840, #F0CB37) but could not parse the July 2025 official brand book PDF due to size constraints.

**How to avoid:** Before committing `@theme` tokens, download the official Scouts QLD brand kit from `scoutsqld.com.au/members/resources/brandhome/` or `scoutsqld.com.au/wp-content/uploads/2020/05/Scouts-Australia-Brand-Book-Jan20.pdf`. Use official hex values. Community values are safe placeholders only.

**Warning signs:** Colours look slightly different from official Scouts QLD website; green appears as emerald rather than the trademark Scouts mid-green.

### Pitfall 3: Cloudflare Pages Node.js Version Mismatch (Historical — Resolved)

**What goes wrong:** Older Cloudflare Pages build image defaulted to Node.js 18.17.1, which Astro 5.8+ no longer supports (requires v20+ or v22+).

**Why it happens:** Legacy build image used Node 18 by default.

**How to avoid:** For NEW projects created after May 2025, Cloudflare Pages automatically uses the v3 build image with Node.js v22 as default. No action required. If the project is connected to an existing Pages project that shows build errors, set `NODE_VERSION = 22` in Cloudflare Pages environment variables (Settings > Environment variables).

**Warning signs:** Build error "Node.js version X is not supported by Astro".

### Pitfall 4: `@import` Order in global.css

**What goes wrong:** Google Fonts `@import url(...)` placed after `@import "tailwindcss"` causes a CSS spec violation. Some build tools silently ignore the font import; others throw a warning.

**Why it happens:** Developer writes `@import "tailwindcss"` first, then adds fonts below it.

**How to avoid:** Google Fonts `@import url(...)` must be the VERY FIRST line in `global.css`. Then `@import "tailwindcss"`. Then `@theme {}`. Then any other custom styles.

**Warning signs:** Custom font not loading despite being imported; DevTools shows font-family falling back to system default.

### Pitfall 5: Putting Images in `public/` Instead of `src/images/`

**What goes wrong:** Phase 3 gallery images placed in `public/` cannot be optimised by Astro's `<Image />` component, resulting in unoptimised JPEG/PNG files served to users.

**Why it happens:** `public/` is intuitive for "static" assets; common in other frameworks.

**How to avoid:** All images that need WebP conversion and responsive srcset must live under `src/images/`. Static assets that should never be processed (e.g., favicons, PDFs) go in `public/`. The CONTEXT.md explicitly mandates `src/images/gallery/` for gallery images.

---

## Code Examples

Verified patterns from official sources:

### Tailwind v4 Installation

```bash
# Source: https://tailwindcss.com/docs/installation/framework-guides/astro
npm install tailwindcss @tailwindcss/vite
```

### astro.config.mjs — Tailwind v4 Vite Plugin

```javascript
// Source: https://tailwindcss.com/docs/installation/framework-guides/astro
import { defineConfig } from 'astro/config';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  vite: {
    plugins: [tailwindcss()],
  },
});
```

### global.css — Full Brand Token Setup

```css
/* Source: https://tailwindcss.com/docs/theme */

/* 1. External font imports FIRST — before @import "tailwindcss" */
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@700;800;900&family=Nunito:wght@400;600;700;800&display=swap");

/* 2. Tailwind base */
@import "tailwindcss";

/* 3. Brand tokens — these become utility classes AND CSS variables */
@theme {
  /* Colors — VERIFY these hex values against official Scouts QLD brand kit before final commit */
  --color-scout-green:      #077840;
  --color-scout-green-dark: #055c30;
  --color-scout-gold:       #F0CB37;
  --color-scout-gold-dark:  #d4a800;

  /* Typography */
  --font-display: "Montserrat", "Arial Black", sans-serif;
  --font-body:    "Nunito", "Arial", sans-serif;
}
```

### Cloudflare Pages GitHub CI/CD — Build Settings

| Setting | Value |
|---------|-------|
| Framework preset | Astro |
| Build command | `npm run build` |
| Build output directory | `dist` |
| Production branch | `main` |
| Node.js version | 22 (automatic on new projects with v3 build image) |

### Astro `<Image />` Component (for Phase 3 reference)

```astro
---
// Source: https://docs.astro.build/en/guides/images/
import { Image } from 'astro:assets';
import heroPhoto from '../images/hero-scouts.jpg';
---
<Image
  src={heroPhoto}
  alt="Scouts cooking around a campfire at Cooroy"
  widths={[400, 800, 1200]}
  sizes="(max-width: 768px) 400px, (max-width: 1024px) 800px, 1200px"
  class="w-full h-full object-cover"
/>
```

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| `@astrojs/tailwind` integration | `@tailwindcss/vite` plugin in `vite.plugins` | Tailwind v4 release (Jan 2025) | No `tailwind.config.js`; tokens in CSS; simpler setup |
| `tailwind.config.js` for design tokens | `@theme {}` in CSS file | Tailwind v4 release (Jan 2025) | All configuration is CSS-first; no JS config file |
| Cloudflare Pages Node 18 default | Node 22 default (v3 build image) | May 2025 | No `NODE_VERSION` env var needed for new projects |
| Cloudflare Auto Minify (broke Astro hydration) | Feature removed entirely | August 5, 2024 | No longer a concern for any new deployment |

**Deprecated/outdated:**
- `@astrojs/tailwind`: Works only with Tailwind v3, not v4. Do not install.
- `tailwind.config.js` for a v4 project: Unnecessary — v4 reads tokens directly from CSS `@theme {}`.
- Cloudflare Auto Minify: Fully removed from Cloudflare's platform; no action needed.

---

## Open Questions

1. **Official Scouts QLD brand hex values**
   - What we know: Community consensus gives `#077840` (green) and `#F0CB37` (gold) as Scouts Australia primary colors from their 2019/2020 brand book. The July 2025 updated brand book exists at `scouts.com.au` but was too large to parse during research.
   - What's unclear: Whether the July 2025 brand update changed the primary green or gold hex values. The QLD-specific brand companion document (v1.0 Mar 2020) was also inaccessible (PDF too large).
   - Recommendation: Before committing final design tokens, download and open `https://scoutsqld.com.au/wp-content/uploads/2020/03/QLD-Brand-Companion-Document-v1.0-Mar-2020-1.pdf` manually in a browser. Use `#077840` and `#F0CB37` as confirmed starting points — they are consistent across multiple independent sources and the Scouts Australia brand book metadata.

2. **Scouts QLD logo / wordmark usage rights**
   - What we know: The brand kit is available at `scoutsqld.com.au/members/resources/brandhome/`. Logos require member login to download. The QLD Brand Resource Centre page exists.
   - What's unclear: Whether a local group website is permitted to use the national/state fleur-de-lis logo, or whether a group-specific badge is required.
   - Recommendation: Contact `brandsupport@scoutsqld.com.au` for permission and asset download. In the meantime, use a text-only wordmark ("Cooroy Scout Group") for the Phase 1 nav header.

3. **Placeholder hero photography**
   - What we know: CONTEXT.md specifies scout stock photos of outdoor/scouting activities until real group photos are available.
   - What's unclear: Specific royalty-free sources approved for this use case.
   - Recommendation: Unsplash (unsplash.com, search "scouts camping") or Pexels provide CC0/royalty-free outdoor youth activity photos. Apply the `alt` text policy from REQUIREMENTS.md (no full children's names; use activity descriptions).

---

## Validation Architecture

### Test Framework

| Property | Value |
|----------|-------|
| Framework | None detected — greenfield project |
| Config file | None — Wave 0 task creates project scaffold |
| Quick run command | N/A — Phase 1 validation is browser/visual |
| Full suite command | N/A |

### Phase Requirements → Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| INFR-01 | Site renders correctly on mobile, tablet, desktop | Manual visual | `npm run dev` + browser DevTools responsive mode | Wave 0 (scaffold) |
| INFR-01 | BaseLayout renders Nav and Footer without errors | Build smoke test | `npm run build` — exits 0, no errors | Wave 0 (scaffold) |
| INFR-02 | Tailwind brand tokens produce correct utility classes | Manual visual | Inspect element in browser; check computed styles | Wave 0 (scaffold) |
| INFR-02 | Brand colours are from official Scouts QLD assets | Manual verification | Confirm hex values match brand kit PDF before commit | Manual only — requires human review of PDF |
| INFR-03 | Push to `main` triggers Cloudflare Pages rebuild | Integration (live) | Push a commit and observe CF Pages dashboard build log | Manual only — requires Cloudflare connected |
| INFR-03 | Site loads over HTTPS on `*.pages.dev` URL | Smoke test (live) | `curl -s -o /dev/null -w "%{http_code}" https://{project}.pages.dev` | Manual only — post-deploy |

### Sampling Rate

- **Per task commit:** `npm run build` — confirms no TypeScript/Astro compilation errors
- **Per wave merge:** `npm run build` + manual browser check of responsive breakpoints
- **Phase gate:** All three success criteria verified manually before `/gsd:verify-work`

### Wave 0 Gaps

- [ ] No project scaffold exists — Wave 0 must run `npm create astro@latest` and install `tailwindcss @tailwindcss/vite`
- [ ] No `astro.config.mjs` — created during scaffold
- [ ] No `src/styles/global.css` — created during scaffold with brand tokens
- [ ] No test runner configured — Phase 1 validation is entirely visual/manual; no automated test framework needed for this phase

*(Phase 1 has no automated unit tests by design — it is infrastructure and visual foundation. Validation is: build succeeds, site loads in browser, Nav/Footer render correctly at all breakpoints, colours are visually correct.)*

---

## Sources

### Primary (HIGH confidence)
- [Tailwind CSS Docs — Astro Framework Guide](https://tailwindcss.com/docs/installation/framework-guides/astro) — `@tailwindcss/vite` setup, exact package names
- [Tailwind CSS Docs — Theme variables](https://tailwindcss.com/docs/theme) — `@theme {}` syntax, namespace conventions, CSS variable output
- [Astro Docs — Layouts](https://docs.astro.build/en/basics/layouts/) — `<slot />` pattern, Props interface, layout nesting
- [Astro Docs — Project Structure](https://docs.astro.build/en/basics/project-structure/) — `src/` vs `public/` conventions, reserved directories
- [Astro Docs — Deploy to Cloudflare](https://docs.astro.build/en/guides/deploy/cloudflare/) — build settings, static vs SSR adapter distinction, Auto Minify warning
- [Cloudflare Pages — Build Image v3 Changelog](https://developers.cloudflare.com/changelog/post/2025-05-30-pages-build-image-v3/) — Node.js v22 default from May 2025

### Secondary (MEDIUM confidence)
- Scouts Australia brand colors: Community consensus (#077840 green, #F0CB37 gold) consistent across SchemeColor.com, flagcolorcodes.com, and multiple Scouts state branch sites — corroborated against the 2019 Scouts Australia Brand Book metadata
- [Tailwind v4 Google Fonts guide](https://hatchet.com.au/blog/how-to-use-google-fonts-in-tailwind-css/) — `@import url()` must precede `@import "tailwindcss"`
- [Cloudflare Community — Auto Minify Deprecation](https://community.cloudflare.com/t/deprecating-auto-minify/655677) — confirmed removed August 5, 2024

### Tertiary (LOW confidence — flag for validation)
- Specific Scouts QLD hex values: #077840 and #F0CB37 are consistent with community sources but NOT verified against the July 2025 official brand book PDF (file too large to parse). Treat as working placeholders until official PDF is reviewed.

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — Verified against official Tailwind and Astro docs
- Architecture: HIGH — Patterns derived from official Astro docs; Tailwind v4 `@theme` verified from official Tailwind docs
- Brand colors: MEDIUM — Consistent community sources, but July 2025 official PDF not parseable; values require human verification against official document
- Cloudflare deployment: HIGH — Official Cloudflare Pages docs + confirmed Node.js v22 default change
- Pitfalls: HIGH — Auto Minify removal confirmed by Cloudflare community; Tailwind deprecation confirmed by official docs

**Research date:** 2026-03-05
**Valid until:** 2026-06-05 (stable stack; Tailwind v4 and Astro 5 APIs unlikely to change significantly; brand colors stable unless Scouts Australia rebrands)
