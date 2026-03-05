# Stack Research

**Domain:** Scout group informational website (static, CMS-editable, contact form)
**Researched:** 2026-03-05
**Confidence:** HIGH (core stack verified via official docs and npm; CMS recommendation MEDIUM due to Sveltia CMS beta status)

---

## Recommended Stack

### Core Technologies

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Astro | 5.18.x (stable) | Static site framework | Decided. Island architecture keeps JS minimal; content collections give type-safe markdown editing that CMS files slot into cleanly. v6 is in beta — stay on v5 stable. |
| Tailwind CSS | 4.2.x | Utility-first CSS | Decided. v4 is the current stable major with a new Vite plugin replacing the deprecated @astrojs/tailwind integration. CSS-first config (@theme) replaces tailwind.config.js. |
| Cloudflare Pages | — | Hosting and CI/CD | Decided. Free tier, global CDN, auto-deploys from GitHub. Build command: `npm run build`, output dir: `dist`. No adapter needed for static output. |

### CMS

**Recommendation: Sveltia CMS over Decap CMS**

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Sveltia CMS | 0.x (beta, v1.0 expected early 2026) | Git-based CMS for volunteer editors | Drop-in replacement for Decap CMS. Uses Decap config files, no migration cost. 5x smaller bundle (300 KB vs 1.5 MB), uses GitHub GraphQL API (faster than Decap's REST), modern UX. Has an official Cloudflare Workers auth script that deploys in 30 seconds. |
| sveltia-cms-auth | — | OAuth handler on Cloudflare Workers | First-party auth solution for Sveltia CMS on Cloudflare Pages. One-click deploy to Workers; no separate auth server needed. |

**Why not Decap CMS:** Netlify officially stopped supporting it. It is no longer actively maintained or adding features. Known for slow load times, 1.5 MB bundle, individual REST calls per content item. Avoid for new projects.

**Why not TinaCMS:** Requires a running backend (TinaCloud or a self-hosted Node.js server with a database) to serve the editing API at runtime. Cloudflare Pages is a static host — running TinaCMS in production means either paying for TinaCloud or managing a separate backend deployment. Overkill for a volunteer-edited informational site with ~4 content updates per year. The GraphQL data layer and visual editing are impressive but unnecessary complexity here.

**Why not Keystatic CMS:** Excellent choice for this use case and a legitimate alternative. Requires either Keystatic Cloud (up to 3 users free) or a GitHub OAuth app. More developer-friendly config (TypeScript schema files rather than YAML). If the developer prefers TypeScript schema over YAML, choose Keystatic over Sveltia CMS. Both are viable.

### Forms

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Web3Forms | — | Contact form email forwarding | Free tier: 250 submissions/month (far exceeds a scout group's needs). No backend required — add an access key to the HTML form. hCaptcha spam protection built in. No monthly fee; generous limits for low-traffic community sites. |

**Why not Formspree:** Free tier is more restrictive; paid plan starts at $10/month. Appropriate for higher-traffic commercial sites, unnecessary cost here.

**Why not Formspark:** One-time payment ($25 for 50,000 credits) is better value than Formspree monthly, but Web3Forms is genuinely free for this volume. Use Formspark if the group later needs Slack/Zapier/Google Sheets integrations.

### Image Optimization

| Technology | Version | Purpose | Why Recommended |
|------------|---------|---------|-----------------|
| Astro `<Image />` / `<Picture />` | Built into Astro 5.x | Responsive image optimization | Built-in. Automatically converts to WebP/AVIF, enforces width/height to prevent CLS, generates srcset. Sharp is the default processing backend and ships with Astro. No extra install needed. |
| astro-pandabox | latest | Lightbox for photo gallery | Lightweight, Astro-native lightbox/gallery component. Integrates with Astro's `<Image />` for optimization + lightbox in one. Simple to configure. |

---

### Supporting Libraries

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| sharp | auto (Astro dep) | Image processing | Required by Astro's image pipeline; installed automatically. With pnpm, install explicitly: `pnpm add sharp`. |
| @tailwindcss/vite | 4.x | Tailwind v4 Vite integration | Required for Tailwind v4 in Astro. Replaces the deprecated @astrojs/tailwind integration. |
| astro-icon | 1.x | SVG icon component | Use when embedding Scouts Queensland logos or UI icons from Iconify (large icon library, tree-shaken at build). |

---

### Development Tools

| Tool | Purpose | Notes |
|------|---------|-------|
| Node.js | Runtime | Use LTS (v22.x as of 2026). Cloudflare Pages uses Node 22 by default. |
| pnpm | Package manager | Faster installs than npm; disk efficient. If using pnpm, explicitly install `sharp` as it is a peer dep Astro doesn't auto-hoist. |
| TypeScript | Type safety | Astro ships with TS support. Use `strict` mode. |
| Prettier + prettier-plugin-astro | Code formatting | Formats `.astro` files. The `prettier-plugin-astro` plugin is required — Prettier does not handle `.astro` without it. |
| ESLint + eslint-plugin-astro | Linting | Catches Astro-specific issues. Optional but useful. |

---

## Installation

```bash
# Create Astro project (use the interactive wizard)
npm create astro@latest

# Add Tailwind v4 (Astro will configure @tailwindcss/vite automatically)
npx astro add tailwind

# Supporting libraries
npm install astro-icon astro-pandabox

# Dev dependencies
npm install -D prettier prettier-plugin-astro

# CMS: Sveltia CMS — no npm install needed.
# Add two files to your project:
#   public/admin/index.html  (load the Sveltia CMS script from CDN)
#   public/admin/config.yml  (content schema configuration)
# Then deploy sveltia-cms-auth as a Cloudflare Worker (one-click deploy).
```

---

## Alternatives Considered

| Recommended | Alternative | When to Use Alternative |
|-------------|-------------|-------------------------|
| Sveltia CMS | Keystatic CMS | If you prefer TypeScript schema files over YAML config; if the team has 3 or fewer editors and wants a free Keystatic Cloud account for simpler auth |
| Sveltia CMS | Decap CMS | Never for new projects. Decap is unmaintained. |
| Sveltia CMS | TinaCMS | If you need visual page-builder editing with block components and are willing to run a backend server or pay for TinaCloud |
| Web3Forms | Formspark | If you need Slack/Zapier/Google Sheets integrations from the form service |
| Web3Forms | Formspree | If you have > 250 form submissions/month and need paid reliability guarantees |
| astro-pandabox | PhotoSwipe (vanilla JS) | If you need a highly customisable lightbox with deep UX control — steeper setup |
| Astro `<Image />` | Cloudflare Images | If the gallery grows very large (hundreds of photos) and you want Cloudflare's CDN image transforms without build-time processing |

---

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| `@astrojs/tailwind` integration | Deprecated as of Astro 5.2. Only supports Tailwind v3. Will not receive updates. | `@tailwindcss/vite` plugin directly in `astro.config.mjs` |
| `tailwind.config.js` | Replaced by CSS-first `@theme` configuration in Tailwind v4. Creating one signals an outdated setup. | `@theme {}` block in your global CSS file |
| Decap CMS | Unmaintained since Netlify dropped support. Open bugs accumulate. 1.5 MB bundle. | Sveltia CMS (drop-in replacement, same YAML config) |
| TinaCMS with static Cloudflare Pages | Requires a Node.js API backend running at runtime to serve the editing UI. Static Pages cannot run Node servers. | Sveltia CMS or Keystatic (both are truly static/git-only) |
| `@astrojs/image` (legacy) | Removed in Astro v3+. No longer exists. | Built-in `astro:assets` (`<Image />` and `<Picture />`) |
| Netlify Forms | Netlify-specific; you are on Cloudflare Pages. | Web3Forms |

---

## Stack Patterns by Variant

**If the group later needs editorial workflow (draft/publish approvals):**
- Upgrade to Sveltia CMS with editorial_workflow enabled in config.yml
- This uses GitHub pull requests as a draft mechanism — no extra cost

**If the gallery grows beyond 50 photos:**
- Use Cloudflare Images ($5/month) instead of build-time Sharp processing
- Cloudflare Pages keeps the static build fast; image transforms happen at edge request time

**If non-technical editors struggle with GitHub-based CMS login:**
- Sveltia CMS free tier (GitHub auth) is the simplest path
- Keystatic Cloud free tier (up to 3 users, no GitHub account required) is the alternative

**If Astro v6 stable releases during development:**
- v6 stabilises experimental features from v5 (SVG optimization, Content Layer improvements)
- Migration is low-risk for a content site; upgrade when stable

---

## Version Compatibility

| Package | Compatible With | Notes |
|---------|-----------------|-------|
| astro@5.18.x | tailwindcss@4.2.x | Use `@tailwindcss/vite` plugin, NOT `@astrojs/tailwind` |
| astro@5.18.x | sharp@0.33.x | Sharp ships as Astro dependency; pnpm users may need explicit install |
| tailwindcss@4.x | @tailwindcss/vite@4.x | Must match major versions; both are in the `tailwindlabs/tailwindcss` monorepo |
| Sveltia CMS | Any Astro version | No npm package; loaded as CDN script in `/public/admin/index.html` |
| Cloudflare Pages | Astro static output | No adapter needed for static output. Only add `@astrojs/cloudflare` adapter if using SSR/server routes. |

---

## Sources

- https://github.com/withastro/astro/releases — Astro v5.18.0 is current stable (March 2026); v6 in beta
- https://astro.build/blog/whats-new-february-2026/ — Astro February 2026 release notes
- https://tailwindcss.com/blog/tailwindcss-v4 — Tailwind v4.0 official announcement (January 2025)
- https://www.npmjs.com/package/tailwindcss — Confirmed 4.2.1 as latest stable (March 2026)
- https://docs.astro.build/en/guides/integrations-guide/tailwind/ — `@astrojs/tailwind` deprecation confirmed; `@tailwindcss/vite` is recommended
- https://github.com/sveltia/sveltia-cms — Sveltia CMS GitHub: beta, 300 KB bundle, framework-agnostic, Decap config compatible
- https://github.com/sveltia/sveltia-cms-auth — Cloudflare Workers auth for Sveltia CMS
- https://decapcms.org/docs/intro/ — Decap CMS documentation; Netlify dropped support confirmed
- https://tina.io/docs/self-hosted/overview — TinaCMS requires backend (Node.js + database) for production
- https://keystatic.com/docs/cloud — Keystatic does not require Cloud; GitHub-only mode is fully supported
- https://web3forms.com/pricing — Free tier: 250 submissions/month, no monthly fee
- https://docs.astro.build/en/guides/images/ — Astro built-in `<Image />` and `<Picture />` components; Sharp default
- https://github.com/SaintSin/astro-pandabox — astro-pandabox lightbox component
- https://developers.cloudflare.com/pages/framework-guides/deploy-an-astro-site/ — Cloudflare Pages Astro build settings (build: `npm run build`, output: `dist`)
- https://staticmania.com/blog/top-git-based-cms — Git-based CMS comparison 2025 (MEDIUM confidence, single source)
- https://bejamas.com/compare/decapcms-vs-tina — DecapCMS vs TinaCMS comparison (MEDIUM confidence)

---

*Stack research for: Cooroy Scout Group Website (static informational site)*
*Researched: 2026-03-05*
