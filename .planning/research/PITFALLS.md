# Pitfalls Research

**Domain:** Scout group / community organisation informational website (Astro + Tailwind + git-based CMS + Cloudflare Pages)
**Researched:** 2026-03-05
**Confidence:** HIGH for CMS/deployment pitfalls (verified against official docs and community reports); MEDIUM for content/UX pitfalls (pattern-based, multiple nonprofit sources agree)

---

## Critical Pitfalls

### Pitfall 1: Decap CMS OAuth Requires a Separate Auth Proxy on Cloudflare Pages

**What goes wrong:**
Decap CMS (and Tina CMS) authenticate via GitHub OAuth. On Netlify this is handled transparently. On Cloudflare Pages, there is no built-in OAuth handler — the developer must deploy a Cloudflare Worker or Cloudflare Function as a GitHub OAuth proxy, configure a GitHub OAuth App, and wire the `base_url` in Decap's `config.yml` to point at the proxy endpoint. Skipping this step means the `/admin` route loads but login silently fails or redirects to Netlify's auth service (which doesn't work for a non-Netlify deployment).

**Why it happens:**
The Decap CMS documentation defaults assume Netlify hosting. "Deploy to Cloudflare Pages" instructions in the Astro docs describe copying the admin assets but don't walk through the OAuth proxy step, so developers believe the integration is complete when it isn't.

**How to avoid:**
Before writing any CMS content schemas, set up the OAuth proxy first and verify that a test editor account can log in and save a file through `/admin`. Use one of the maintained Cloudflare-specific OAuth proxy repos (e.g., `i40west/netlify-cms-cloudflare-pages` or `sterlingwes/decap-proxy`). Ensure `site_domain` and `base_url` values in `config.yml` do NOT end with a trailing slash — this causes a redirect loop.

**Warning signs:**
- Login button on `/admin` page does nothing or redirects to a Netlify URL
- GitHub OAuth App callback URL is set to `localhost` or is missing entirely
- `base_url` in `config.yml` still points to `https://api.netlify.com`

**Phase to address:**
CMS integration phase — must be proven working end-to-end (login → edit → git commit → rebuild) before any content editing is handed to volunteers.

---

### Pitfall 2: Tina Cloud Free Tier Caps at 2 Users — Wrong Choice for Volunteer Groups

**What goes wrong:**
TinaCMS's cloud-hosted backend (Tina Cloud) limits the free plan to 2 users and 1 project. A scout group with a leader, assistant leader, and committee member needing CMS access immediately exceeds this. Paid plans (Team and above) add cost that a volunteer group may not budget for.

**Why it happens:**
The self-hosted Tina option exists but requires a Node.js serverless environment that doesn't map cleanly onto a purely static Cloudflare Pages deployment. The developer assumes free = unlimited volunteer editors.

**How to avoid:**
Use Decap CMS instead of Tina for this project. Decap is fully self-hosted (no SaaS backend), has no user count limits, and is documented for the exact Astro + Cloudflare Pages stack. If Tina is preferred for its visual editing, add up expected editors before committing: if the count ever exceeds 2, budget for the paid plan or switch to Decap.

**Warning signs:**
- More than 2 people need `/admin` access
- Project context names more than 2 content editors (the developer's wife + group leader = already at the limit)
- Tina Cloud dashboard shows "user limit reached" error

**Phase to address:**
CMS selection/setup phase — decide CMS before any content schemas are written; switching later requires migrating all schema definitions.

---

### Pitfall 3: Images in `/public` Are Not Optimised — Gallery Page Will Be Slow

**What goes wrong:**
Volunteers uploading photos through the CMS or the developer adding gallery images directly into the `public/` folder results in full-resolution JPEGs being served without resizing, format conversion (WebP/AVIF), or lazy loading. A gallery page with 20 photos at 3-5 MB each will score poorly on Lighthouse and be unusable on mobile data connections. Astro's `<Image>` component only processes images placed in `src/` — files in `public/` bypass all optimisation.

**Why it happens:**
`public/` is the intuitive place to "put files the site needs," and the Astro docs describe it as the location for assets that "do not require processing." Developers default to it before understanding the distinction.

**How to avoid:**
Place all gallery images in `src/assets/` and use Astro's `<Image>` or `<Picture>` components, which automatically generate WebP/AVIF, enforce explicit width/height (preventing Cumulative Layout Shift), and produce `loading="lazy"` by default. For CMS-uploaded images, configure the CMS media folder to map to `src/assets/` where possible, or use Cloudflare Images (free tier: 100k transformations/month) as an external image CDN to serve optimised variants via URL parameters.

**Warning signs:**
- Gallery images are referenced with `/photos/...` paths rather than imported with `import` statements or `src/assets/` paths
- Lighthouse "Serve images in next-gen formats" warning appears
- Page weight exceeds 2 MB on the gallery page

**Phase to address:**
Gallery/media phase — establish the image pipeline pattern before any real photos are added. Setting the pattern late means re-uploading or re-referencing every image.

---

### Pitfall 4: Placeholder Content Ships to Production and Stays There

**What goes wrong:**
The site launches with "Lorem ipsum" section descriptions, placeholder meeting times ("TBC"), and a "coming soon" events page. The group leader is away at launch time, so placeholders remain for months. Parents visit, see incomplete information, and conclude the group is inactive. The content is "updated later" but later never comes because there is no process or deadline.

**Why it happens:**
It is technically correct that a site can launch with placeholder content — the features all work. But a community org site's primary job is converting interested parents into members, and placeholder content directly destroys that conversion. There is no automated system to prompt content owners to fill in gaps.

**How to avoid:**
Before launch, define a content minimum: each section page must have real meeting time, real location, and at least a paragraph description. Use the old website (Wayback Machine) as a content source for the initial fill. For the events page, a real term program PDF or a simple list of activities is sufficient — "events coming soon" is not acceptable as a shipped state. Schedule a content review session with the group leader and the developer's wife before considering the site done.

**Warning signs:**
- Any page contains "Lorem ipsum" or "TBC" text
- Meeting times section shows placeholder values
- The events/term program page is empty or redirects to a placeholder
- The contact form's recipient email address is still a developer email

**Phase to address:**
Content population phase — treat content completeness as a launch gate, not an afterthought.

---

### Pitfall 5: Form Spam Arrives Immediately After Launch — No Protection Configured

**What goes wrong:**
A Formspree or Formspark contact form with no CAPTCHA or honeypot field gets hit by spam bots within hours of the domain going live. Modern spam bots are aware of the standard `_gotcha` honeypot field name. The group leader's inbox fills with spam, they conclude the form is broken, and they stop checking it.

**Why it happens:**
Developers assume the default Formspree form is "good enough" for a small community site. The invisible honeypot provides minimal protection against sophisticated bots that specifically target common static site form providers.

**How to avoid:**
Configure both the built-in Formspree/Formspark honeypot AND enable their reCAPTCHA or Turnstile option (Cloudflare Turnstile is a free, GDPR-friendlier alternative to reCAPTCHA). Use a custom honeypot field name (not `_gotcha`) to avoid bot detection of the standard name. Formspark supports Cloudflare Turnstile natively. Set up Formspree's email notification rules to filter obvious spam keywords.

**Warning signs:**
- The form has no `<input type="hidden" name="[custom-name]" />` honeypot field
- No CAPTCHA widget visible or configured in the form provider dashboard
- First spam submission arrives within 24 hours of launch

**Phase to address:**
Contact/join form phase — configure spam protection before the form is publicly linked anywhere.

---

### Pitfall 6: Cloudflare Auto Minify Breaks Astro Hydration

**What goes wrong:**
Cloudflare's "Auto Minify" feature (available in Speed settings) minifies HTML/CSS/JS at the CDN edge. This can corrupt Astro's client-side hydration markers for any interactive islands (e.g., a JavaScript-powered lightbox in the gallery, or an animated CTA). The result is broken interactivity with console errors like "Hydration completed but contains mismatches."

**Why it happens:**
Auto Minify is presented as a "free performance win" in the Cloudflare dashboard and is often enabled by default or enabled by developers wanting better Lighthouse scores. The interaction with Astro's hydration comments is non-obvious.

**How to avoid:**
In the Cloudflare dashboard, under Speed > Optimization > Content Optimization, disable Auto Minify for HTML (CSS and JS are safer). Astro performs its own minification at build time — double minification at the CDN edge is unnecessary and harmful. Document this setting in the project README so it is not re-enabled.

**Warning signs:**
- Interactive components work in local dev but are broken on the live Cloudflare URL
- Console shows hydration mismatch errors only in production
- Auto Minify is enabled in Cloudflare Speed settings

**Phase to address:**
Deployment/CI-CD setup phase — verify Auto Minify is off before running any Lighthouse audits.

---

### Pitfall 7: Scouts Queensland Branding Used Without Checking Licence Restrictions

**What goes wrong:**
The developer imports Scouts Queensland logos and brand assets from the existing website or the Wayback Machine and uses them freely. Scouts Queensland (and Scouts Australia) have brand usage guidelines that restrict how logos can be reproduced, modified, or combined with third-party design elements. Using unapproved colour variations, applying logos over conflicting backgrounds, or combining the fleur-de-lis with non-approved design treatments can breach the licence.

**Why it happens:**
The project description says "Scouts Queensland provides standard branding as a foundation" which implies open use. In practice, Scouts organisations have formal brand manuals with restrictions.

**How to avoid:**
Obtain the official Scouts Queensland Digital Brand Guide before designing any page templates. Contact the state body or the group leader to confirm which logo files and colour codes are approved for group-level websites. Use only official HEX colour values from the brand guide in the Tailwind config.

**Warning signs:**
- Logo files sourced from the Wayback Machine rather than obtained directly from Scouts QLD
- Brand colours defined by eyedropper-sampling the old website rather than from official brand documentation
- Fleur-de-lis or wordmark modified, re-coloured, or placed on an unapproved background

**Phase to address:**
Design/branding setup phase — confirm asset permissions before design tokens are committed to the Tailwind config.

---

## Technical Debt Patterns

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Images in `public/` instead of `src/assets/` | Faster initial setup, CMS uploads work naturally | Gallery is unoptimised; every image served at full resolution | Only acceptable for SVG icons that don't need resizing |
| Hardcoded meeting times in page components | No CMS schema needed for initial build | Each term change requires a code deploy, not a CMS edit | Never — meeting times must be CMS-editable from day one |
| Using `_gotcha` default honeypot name | Zero extra config | Known to spam bots; ineffective protection | Never |
| Placeholder content at launch | Ship faster | Parents conclude group is inactive; harms recruitment | Never — at minimum, meeting times and contact info must be real |
| Inline Tailwind colour values instead of design tokens | Faster early styling | Scouts QLD brand colours scattered across files; one brand update = global search-replace | Never — define CSS variables or `tailwind.config` tokens for brand colours immediately |
| Decap CMS `config.yml` without explicit media_folder | CMS works locally | CMS uploads go to wrong path; images break after deploy | Never — set `media_folder` and `public_folder` explicitly on first setup |

---

## Integration Gotchas

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Decap CMS + Cloudflare Pages | Assuming Netlify Identity works; copy-pasting Netlify docs | Deploy a Cloudflare Worker OAuth proxy first; configure GitHub OAuth App with the correct callback URL |
| Decap CMS `config.yml` | Trailing slash on `base_url` or `site_domain` | Neither field should end in `/` — causes a redirect loop on login |
| Formspree free plan + AJAX | Using `fetch()` to submit the form expecting JSON response | Formspree AJAX/JSON only works on paid plans; use standard HTML form submit with `_next` redirect on the free plan |
| Astro `<Image>` + CMS-uploaded images | CMS media paths use `/public/uploads/...` but `<Image>` expects `src/` imports | Use `<img>` with manual `loading="lazy"` and `width`/`height` for CMS-managed images, OR store media in `src/assets/` and reference by path |
| Cloudflare Pages build | Default `npm run build` command not set; Cloudflare picks wrong output directory | Explicitly set build command to `npm run build` and output directory to `dist` in Pages settings |
| TinaCMS + Astro | Visual editor wraps pages in React, adding significant JS to every page | Only use Tina if visual editing is essential; Decap avoids this entirely |
| Formspree spam | Standard `_gotcha` honeypot field only | Use a custom-named hidden field AND enable Cloudflare Turnstile in the form provider dashboard |

---

## Performance Traps

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Gallery images in `public/` | Lighthouse "Serve images in next-gen formats" red, slow mobile page load | Move images to `src/assets/`, use `<Image>` component | With any photo; 1 unoptimised 4 MB phone photo is enough |
| Importing many large images at build time in `src/assets/` | Cloudflare Pages build timeout (25 min free tier limit) | Keep gallery images under 500 KB each before committing; use Cloudflare Images CDN for large collections | Beyond ~50 unoptimised photos (~100 MB) |
| Cloudflare Auto Minify enabled | Astro hydration errors in production; broken interactive components | Disable Auto Minify for HTML in Cloudflare Speed settings | Immediately on any page with a JS island |
| No `width`/`height` on `<img>` elements | Cumulative Layout Shift (CLS) score fails | Always set explicit dimensions or use Astro `<Image>` which infers them | On any image; CLS is a Core Web Vital |
| All JS in `<script>` without `defer` or `type="module"` | Render-blocking scripts slow initial paint | Astro handles this automatically for `.astro` files; only an issue if raw `<script>` tags are added manually | Immediately |

---

## Security Mistakes

| Mistake | Risk | Prevention |
|---------|------|------------|
| Recipient email hardcoded in HTML form `action` attribute | Email address scraped by bots, resulting in spam to the group leader's inbox | Use Formspree/Formspark — the recipient email is stored server-side in the provider dashboard, not in HTML |
| CMS admin route publicly listed in sitemap.xml | Invites brute force attempts on the `/admin` login | Exclude `/admin` from sitemap; add `<meta name="robots" content="noindex">` to the admin route |
| GitHub OAuth App client secret committed to repo | Exposes the ability to impersonate the OAuth app | Store OAuth client secret as an environment variable in Cloudflare Pages settings only; never in `config.yml` or any tracked file |
| Outdated Decap CMS version | Potential XSS in the admin UI | Pin Decap version and update on each project maintenance pass; Decap updates are infrequent but do patch security issues |

---

## UX Pitfalls

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| "Join Us" call to action buried below the fold | Parents who want to enrol their child leave without finding the next step | Place a prominent CTA in the hero section with a direct link to the contact/join form |
| Age ranges in section descriptions only (e.g., "Scouts 11-14") without conversion copy | Parents with a 10-year-old don't know if their child qualifies for Cubs or Scouts | Add a simple age-to-section lookup table or selector widget on the sections overview page |
| Contact form with no confirmation message | User submits and wonders if it worked; submits again; group leader gets duplicate messages | Always show an inline success message or redirect to a thank-you page after form submission |
| Mobile navigation hamburger menu with no keyboard/focus management | Users on mobile assistive technology can't navigate the site | Test nav menu with keyboard Tab and Enter before launch; use a standard accessible disclosure pattern |
| Photo gallery without captions or alt text | Screen reader users get no context; SEO misses keyword opportunities | Every gallery image needs a descriptive alt attribute; captions help sighted users identify activities |
| Meeting location as text only — no map link | Parents unfamiliar with Cooroy can't find the hall | Include a Google Maps or Apple Maps link alongside the address, or embed a lightweight static map image |

---

## "Looks Done But Isn't" Checklist

- [ ] **CMS login:** The `/admin` route loads but does OAuth actually complete and allow saving? Verify a test edit commits to the git repo before handing off to volunteers.
- [ ] **Contact form:** The form renders but does it actually deliver emails? Test end-to-end with a real submission and verify arrival in the group leader's inbox.
- [ ] **Contact form spam protection:** Honeypot field is present and CAPTCHA is configured in the form provider dashboard — not just assumed to be default.
- [ ] **Images:** Are images using Astro's `<Image>` component (or equivalent) and NOT being served unoptimised from `public/`?
- [ ] **Meeting times / location:** Are these real values from the actual group, not placeholder text?
- [ ] **Section pages:** Does each of the four section pages (Joeys, Cubs, Scouts, Venturers) have real content including meeting day/time?
- [ ] **404 page:** Is there a custom `404.astro` page? Without it, Cloudflare serves the home page on missing routes.
- [ ] **Alt text:** Do all non-decorative images have meaningful alt text (not keyword-stuffed, not empty)?
- [ ] **Mobile navigation:** Does the hamburger menu work correctly with keyboard-only navigation?
- [ ] **Cloudflare Auto Minify:** Is it confirmed OFF in the Cloudflare Speed dashboard?
- [ ] **CMS config media paths:** Are `media_folder` and `public_folder` explicitly set in `config.yml`?
- [ ] **Scouts QLD brand assets:** Were logo files and colour codes obtained from official Scouts QLD sources (not the Wayback Machine)?

---

## Recovery Strategies

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Decap OAuth not working after launch | MEDIUM | Deploy the Cloudflare Worker proxy, update GitHub OAuth App callback URL, update `config.yml` `base_url`, redeploy |
| Gallery images all in `public/` and unoptimised | MEDIUM | Move images to `src/assets/`, update all references to use `<Image>` component, re-test gallery; cannot be automated |
| Stale placeholder content at launch | LOW-MEDIUM | Schedule a content session with the group leader; all section/meeting info can be updated via CMS without a code deploy |
| Spam flooding the contact form inbox | LOW | Enable Cloudflare Turnstile in Formspree/Formspark dashboard; add custom honeypot field; takes ~30 minutes |
| Cloudflare Auto Minify breaking hydration | LOW | Disable Auto Minify in Cloudflare Speed settings; one toggle, takes 2 minutes |
| Wrong Tina CMS choice (exceeds 2-user free tier) | HIGH | Migrate all content schemas from Tina's GraphQL format to Decap CMS YAML config; likely requires rewriting content collection queries throughout the site |
| Hardcoded meeting times requiring code deploys | MEDIUM | Add CMS schema fields for meeting day/time/location; update page templates to read from CMS; re-enter values |

---

## Pitfall-to-Phase Mapping

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Decap CMS OAuth not working on Cloudflare Pages | CMS setup phase | Login → edit → save → confirm git commit exists; test with a non-developer GitHub account |
| Tina CMS free tier user cap | CMS selection (before any schema work) | Confirm chosen CMS has no per-user cost ceiling for the expected number of editors |
| Unoptimised images in `public/` | Gallery/media phase | Lighthouse audit of the gallery page; all images should be WebP/AVIF and score >90 on Performance |
| Placeholder content at launch | Content population phase (launch gate) | No page contains "Lorem ipsum", "TBC", or placeholder meeting times |
| Form spam with no protection | Contact/join form phase | Submit a test spam payload; confirm it is blocked or filtered |
| Cloudflare Auto Minify | Deployment/CI-CD setup phase | Disable Auto Minify; verify interactive components work on live Cloudflare URL |
| Scouts QLD brand assets from unauthorised sources | Design/branding setup phase | Confirm logo files and hex codes came from official Scouts QLD documentation |
| No custom 404 page | Deployment/CI-CD setup phase | Navigate to a non-existent URL on the live domain; confirm a custom 404 page is shown |
| Accessibility failures (alt text, contrast, nav) | All UI phases (ongoing) | Run axe DevTools or Lighthouse Accessibility audit; target score >90 before launch |

---

## Sources

- [Decap CMS + Cloudflare Pages OAuth setup (biralo.studio, 2024)](https://biralo.studio/2024/11/17/decap-cms-with-cloudflare)
- [Decap CMS external OAuth clients docs](https://decapcms.org/docs/external-oauth-clients/)
- [i40west/netlify-cms-cloudflare-pages — OAuth proxy repo](https://github.com/i40west/netlify-cms-cloudflare-pages)
- [Decap CMS & Astro — official Astro docs](https://docs.astro.build/en/guides/cms/decap-cms/)
- [TinaCMS pricing — tina.io/pricing](https://tina.io/pricing)
- [TinaCMS self-hosted overview — tina.io/docs/self-hosted/overview](https://tina.io/docs/self-hosted/overview)
- [Astro Images — official docs](https://docs.astro.build/en/guides/images/)
- [Astro project structure — public/ vs src/assets/](https://docs.astro.build/en/basics/project-structure/)
- [Cloudflare Pages Astro deployment guide](https://developers.cloudflare.com/pages/framework-guides/deploy-an-astro-site/)
- [Cloudflare Pages build image / Node v22 default (May 2025)](https://developers.cloudflare.com/changelog/post/2025-05-30-pages-build-image-v3/)
- [Cloudflare Pages known issues](https://developers.cloudflare.com/pages/platform/known-issues/)
- [Formspree honeypot spam filtering docs](https://help.formspree.io/hc/en-us/articles/360013580813-Honeypot-spam-filtering)
- [Formspark spam protection docs](https://documentation.formspark.io/setup/spam-protection.html)
- [Formspree CORS + AJAX on free plan (GitHub issue)](https://github.com/okkur/syna-start/issues/45)
- [WCAG 2.2 compliance for nonprofits — Foundation Group (2025)](https://www.501c3.org/wcag-compliant-nonprofit-website/)
- [Nonprofit website stale content pattern — Morweb](https://morweb.org/post/Nonprofit-Website-Engagement-Whats-Going-Wrong)
- [Astro build time optimisation — antstack.com](https://www.antstack.com/blog/how-we-cut-astro-build-time-from-30-minutes-to-5-minutes-83-faster/)
- [How to use Astro's Image Optimization with TinaCMS — joschua.io](https://joschua.io/posts/2023/08/16/how-to-use-astro-assets-with-tina-cms/)

---
*Pitfalls research for: Scout group / community organisation informational website (Astro + Tailwind + Decap/Tina CMS + Cloudflare Pages)*
*Researched: 2026-03-05*
