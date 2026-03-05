# Phase 2: Content Pages - Research

**Researched:** 2026-03-05
**Domain:** Astro 5 pages, Tailwind v4 design tokens, Web3Forms contact form, Cloudflare Turnstile/hCaptcha spam protection, Scouts QLD branding update
**Confidence:** HIGH (Astro patterns, Web3Forms) / MEDIUM (Scouts QLD section colors — no official hex specification found, using uniform color descriptions)

---

<phase_requirements>
## Phase Requirements

| ID | Description | Research Support |
|----|-------------|-----------------|
| HOME-01 | User sees hero section with group intro and prominent "Join Us" CTA | Hero already exists in index.astro placeholder — needs real content and correct brand colors updated (scout-blue replacing scout-green) |
| HOME-02 | User sees overview of available sections with links to section pages | Section cards already on index.astro — needs section-specific colors and links to individual /sections/[section] pages |
| ABUT-01 | User can view About/History page with group background and scouting info | New src/pages/about.astro file needed; inherits BaseLayout; static content |
| CONT-01 | User can submit inquiry via contact form (name, child's age, email) | Web3Forms + Astro static form with fetch API; no backend needed |
| CONT-02 | Form submissions forwarded to group email via Web3Forms | Web3Forms free tier (250/mo); hidden access_key field; confirmed for static sites |
| CONT-03 | Form includes spam protection (Cloudflare Turnstile or hCaptcha) | Web3Forms hCaptcha zero-config available on free plan — use this, not manual Turnstile |
| SECT-01 | User can view Joeys page with age range (5-7), meeting time/location, description | New src/pages/sections/joeys.astro; section-specific tan/light color theme |
| SECT-02 | User can view Cubs page with age range (8-10), meeting time/location, description | New src/pages/sections/cubs.astro; yellow section theme |
| SECT-03 | User can view Scouts page with age range (11-14), meeting time/location, description | New src/pages/sections/scouts.astro; green section theme |
| SECT-04 | User can view Venturers page with age range (15-17), meeting time/location, description | New src/pages/sections/venturers.astro; maroon section theme |
| SECT-05 | Each section page shows activity previews (what a typical night looks like) | Static content block on each section page; structured as hardcoded arrays in Astro frontmatter |
| SECT-06 | User can see age progression pathway (Joeys → Cubs → Scouts → Venturers) overview | Shared SectionProgression.astro component used on all section pages; horizontal timeline or step bar |
| EVNT-01 | User can view current term program/activities on events page | New src/pages/events.astro; structured data array in frontmatter; at least one real term entry |
</phase_requirements>

---

## Summary

Phase 2 builds all public-facing content pages on top of the Phase 1 foundation: a finalised homepage, four section pages (Joeys/Cubs/Scouts/Venturers), an About page, a Contact/Join page with live form submission, and an Events/Term Program page. All pages inherit `BaseLayout.astro` from Phase 1.

**The most important Phase 2 task is updating the brand design tokens.** The user has confirmed that the current scout-green (#077840) and scout-gold (#F0CB37) tokens do not match what scoutsqld.com.au actually uses. Web inspection of scoutsqld.com.au confirms their primary accent color is `#2EA3F2` (a bright cornflower blue) with a dark navy secondary (`#212C66`). These must replace the current tokens in `global.css` before any page content is built — otherwise all pages will need visual rework.

The contact form uses **Web3Forms** (already decided in STATE.md). The free tier covers 250 submissions/month with hCaptcha spam protection at no cost — exactly what CONT-02 and CONT-03 require. The implementation is pure HTML + vanilla JavaScript fetch with no framework dependency, which fits the static Astro approach perfectly.

Section pages each get their own color identity matching the Scouts Australia uniform shoulder color: tan for Joeys, yellow for Cubs, green for Scouts, and maroon for Venturers. These are defined as additional Tailwind v4 `@theme` tokens alongside the updated primary blue, so the section identity is expressed through utility classes like `bg-section-joeys` etc.

**Primary recommendation:** Update `@theme` tokens first (blue primary, navy accent, 4 section colors). Then build pages in dependency order: shared components first, then individual pages. Contact form last (requires Web3Forms API key from group leader).

---

## Standard Stack

### Core (Phase 1 — already installed)

| Library | Version | Purpose | Why Standard |
|---------|---------|---------|--------------|
| astro | 5.x | Static pages via file-based routing | Already in use; no new dependency |
| tailwindcss | 4.x | Section color tokens, utility classes | Already in use; add new @theme tokens |
| @tailwindcss/vite | 4.x | Vite plugin for Tailwind v4 | Already configured; no change needed |

### New for Phase 2

| Library | Version | Purpose | When to Use |
|---------|---------|---------|-------------|
| Web3Forms API | N/A (external API, no npm package) | Form-to-email service for contact form | Required for CONT-01, CONT-02 |
| hCaptcha (via Web3Forms) | N/A (Web3Forms provides zero-config) | Spam protection | Required for CONT-03; zero-config on Web3Forms free plan |

### No New Installations Needed

Phase 2 adds zero npm packages. All functionality comes from:
1. Additional `.astro` page files (Astro file routing)
2. Additional `@theme` tokens in `global.css`
3. Vanilla JavaScript fetch for the contact form (no library)
4. External Web3Forms API (HTTPS POST, no SDK)

**Web3Forms API Key:** Requires one-time registration at `https://web3forms.com/#start` with the group email address. Returns a public API access key (not a secret) that goes in a hidden form field.

---

## Architecture Patterns

### Recommended Project Structure After Phase 2

```
src/
├── components/
│   ├── Nav.astro                    # (existing) — no changes needed
│   ├── Footer.astro                 # (existing) — no changes needed
│   └── SectionProgression.astro    # NEW — age pathway Joeys→Cubs→Scouts→Venturers
├── layouts/
│   └── BaseLayout.astro             # (existing) — no changes needed
├── pages/
│   ├── index.astro                  # UPDATE — real content + corrected brand colors
│   ├── about.astro                  # NEW — group history and scouting background
│   ├── events.astro                 # NEW — term program with real data
│   ├── join.astro                   # NEW — contact/inquiry form (Web3Forms)
│   └── sections/
│       ├── index.astro              # NEW — sections overview with SECT-06 progression
│       ├── joeys.astro              # NEW — Joey section page (age 5-7, tan theme)
│       ├── cubs.astro               # NEW — Cubs section page (age 8-10, yellow theme)
│       ├── scouts.astro             # NEW — Scouts section page (age 11-14, green theme)
│       └── venturers.astro          # NEW — Venturers section page (age 15-17, maroon theme)
├── styles/
│   └── global.css                   # UPDATE — replace scout-green/gold with scout-blue/navy + add section colors
└── images/
    └── gallery/                     # (untouched — Phase 3)
```

**Route map:**
| URL | File | Requirement |
|-----|------|-------------|
| `/` | `pages/index.astro` | HOME-01, HOME-02 |
| `/about` | `pages/about.astro` | ABUT-01 |
| `/events` | `pages/events.astro` | EVNT-01 |
| `/join` | `pages/join.astro` | CONT-01, CONT-02, CONT-03 |
| `/sections` | `pages/sections/index.astro` | SECT-06, HOME-02 |
| `/sections/joeys` | `pages/sections/joeys.astro` | SECT-01, SECT-05 |
| `/sections/cubs` | `pages/sections/cubs.astro` | SECT-02, SECT-05 |
| `/sections/scouts` | `pages/sections/scouts.astro` | SECT-03, SECT-05 |
| `/sections/venturers` | `pages/sections/venturers.astro` | SECT-04, SECT-05 |

Note: Use individual files (not `[section].astro` dynamic route) because each page has distinct content, layout, and section colors. Static individual files are simpler and more maintainable for a 4-page set.

### Pattern 1: Updated Brand Design Tokens (global.css)

**What:** Replace the Phase 1 scout-green/scout-gold tokens with scout-blue/scout-navy that match scoutsqld.com.au, and add 4 section-specific color tokens.

**When to use:** This is the FIRST task in Phase 2 — all subsequent pages depend on the correct tokens.

**Token source:** `#2EA3F2` confirmed via direct web inspection of `scoutsqld.com.au/join-scouts/` CSS. `#212C66` is the user-provided navy accent (from the user's visual inspection of scoutsqld.com.au). Section colors (tan, yellow, green, maroon) are from official Scouts Australia uniform specifications — uniform shoulder/yoke colors per section.

```css
/* src/styles/global.css — Phase 2 update */
/* Source: scoutsqld.com.au CSS inspection + Scouts Australia uniform standards */

@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@700;800;900&family=Nunito:wght@400;600;700;800&display=swap");

@import "tailwindcss";

@theme {
  /* === PRIMARY BRAND COLORS (updated to match scoutsqld.com.au) === */
  --color-scout-blue:       #2EA3F2;  /* Primary blue — confirmed from scoutsqld.com.au CSS */
  --color-scout-blue-dark:  #1a85d0;  /* Hover / darker shade */
  --color-scout-navy:       #212C66;  /* Accent navy — user-confirmed from scoutsqld.com.au */
  --color-scout-navy-dark:  #151d4a;  /* Hover / darker navy */

  /* === LEGACY TOKENS (kept for backward compat during Phase 2 — remove in Phase 3) === */
  --color-scout-green:      #077840;  /* Was primary — now only used for Scouts section */
  --color-scout-green-dark: #055c30;

  /* === SECTION COLORS (uniform shoulder/yoke colors per Scouts Australia standards) === */
  --color-section-joeys:         #C8956C;  /* Tan — Joey uniform yoke color */
  --color-section-joeys-dark:    #a8724a;
  --color-section-cubs:          #F5C400;  /* Yellow — Cubs uniform yoke color */
  --color-section-cubs-dark:     #d4a800;
  --color-section-scouts:        #077840;  /* Green — Scouts uniform yoke color (same as old primary) */
  --color-section-scouts-dark:   #055c30;
  --color-section-venturers:     #8B1A2E;  /* Maroon — Venturers uniform yoke color */
  --color-section-venturers-dark:#6b1222;

  /* === TYPOGRAPHY (unchanged from Phase 1) === */
  --font-display: "Montserrat", "Arial Black", sans-serif;
  --font-body:    "Nunito", "Arial", sans-serif;
}
```

**Important:** After updating global.css, update Nav.astro and Footer.astro to use `bg-scout-blue` / `bg-scout-navy` instead of `bg-scout-green`. Also update index.astro hero section and CTA banner classes.

**Section color confidence note:** Tan hex `#C8956C` and maroon `#8B1A2E` are reasonable approximations of the uniform colors (no official hex found in public sources). Yellow `#F5C400` and Green `#077840` match the previous token and Scouts Australia brand consensus. The user should visually verify against actual uniforms or official brand resources before final commit.

### Pattern 2: Static Individual Page File (section page)

**What:** Each section page is a standalone `.astro` file with page-specific data defined in the frontmatter. No dynamic routing needed.

**When to use:** When 4 pages share identical layout structure but have different colors, content, and data. Individual files are preferred over `[section].astro` because each page's content is genuinely unique (different meeting times, descriptions, age ranges).

```astro
---
// src/pages/sections/joeys.astro
// Source: https://docs.astro.build/en/basics/astro-pages/
import BaseLayout from '../../layouts/BaseLayout.astro';
import SectionProgression from '../../components/SectionProgression.astro';

const section = {
  name: 'Joeys',
  ageRange: '5–7 years',
  colorBg: 'bg-section-joeys',
  colorText: 'text-white',  // or text-amber-900 if tan is too light
  meetingDay: 'Wednesday',
  meetingTime: '5:00 PM – 6:30 PM',
  meetingLocation: 'Cooroy Scout Hall, [Address]',
  description: 'The Joey Scout section is the beginning of the Scouting adventure...',
  activities: [
    'Games and group activities',
    'Nature crafts and simple science experiments',
    'Community service and helping others',
    'Sharing stories from the Billy (the Joey totem)',
  ],
  typicalNight: 'A typical Joey night starts with the Promise ceremony...',
};
---

<BaseLayout title={`${section.name} — Cooroy Scouts`} description="...">
  <!-- Section hero with section-specific color -->
  <section class={`${section.colorBg} text-white`}>
    ...
  </section>

  <!-- Meeting details -->
  ...

  <!-- Age progression pathway (shared component) -->
  <SectionProgression current="joeys" />
</BaseLayout>
```

### Pattern 3: SectionProgression Component

**What:** A shared horizontal step component that shows the 4 sections in age order with the current section highlighted. Fulfills SECT-06.

**When to use:** Rendered on every section page AND on the `/sections` overview page.

```astro
---
// src/components/SectionProgression.astro
interface Props {
  current?: 'joeys' | 'cubs' | 'scouts' | 'venturers';
}

const { current } = Astro.props;

const steps = [
  { id: 'joeys',     label: 'Joeys',      age: '5–7',   href: '/sections/joeys',     color: 'bg-section-joeys'     },
  { id: 'cubs',      label: 'Cubs',       age: '8–10',  href: '/sections/cubs',      color: 'bg-section-cubs'      },
  { id: 'scouts',    label: 'Scouts',     age: '11–14', href: '/sections/scouts',    color: 'bg-section-scouts'    },
  { id: 'venturers', label: 'Venturers',  age: '15–17', href: '/sections/venturers', color: 'bg-section-venturers' },
];
---
<nav aria-label="Age progression pathway" class="py-8 bg-gray-50">
  <div class="max-w-4xl mx-auto px-4">
    <h2 class="font-display font-black text-xl text-scout-navy mb-4 text-center">
      The Scouting Journey
    </h2>
    <ol class="flex flex-col sm:flex-row items-center gap-2">
      {steps.map((step, i) => (
        <li class="flex items-center gap-2 flex-1">
          <a
            href={step.href}
            class:list={[
              step.color,
              'flex-1 text-center py-3 px-4 rounded-lg font-display font-bold text-sm transition-opacity',
              current === step.id ? 'opacity-100 ring-2 ring-offset-2 ring-scout-navy' : 'opacity-70 hover:opacity-100'
            ]}
          >
            <div>{step.label}</div>
            <div class="text-xs font-normal opacity-80">{step.age} yrs</div>
          </a>
          {i < steps.length - 1 && (
            <span class="hidden sm:block text-gray-400 font-bold">→</span>
          )}
        </li>
      ))}
    </ol>
  </div>
</nav>
```

### Pattern 4: Web3Forms Contact Form

**What:** An HTML form that POSTs to the Web3Forms API endpoint via JavaScript fetch. No server needed. Includes hCaptcha for spam protection (zero-config on Web3Forms free plan).

**When to use:** This is the only pattern for the contact/join page. Web3Forms handles email forwarding; hCaptcha handles spam bots.

**Setup required:** Visit `https://web3forms.com/#start`, enter group email, receive access key via email. The access key is public (not secret) and goes in a hidden input field.

```astro
---
// src/pages/join.astro
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="Join Us" description="Get in touch with Cooroy Scout Group...">
  <section class="py-16 bg-white">
    <div class="max-w-2xl mx-auto px-4">
      <h1 class="font-display font-black text-4xl text-scout-navy mb-4">Join Cooroy Scouts</h1>

      <!-- Result message div (shown/hidden by JS) -->
      <div id="result" class="hidden mb-4 p-4 rounded-lg text-sm font-semibold"></div>

      <form id="contact-form" action="https://api.web3forms.com/submit" method="POST">
        <!-- Web3Forms required hidden fields -->
        <input type="hidden" name="access_key" value="YOUR_ACCESS_KEY_HERE" />
        <input type="hidden" name="subject" value="New Scouts Inquiry from Cooroy Scouts Website" />

        <!-- Honeypot field — bots fill this, humans don't see it -->
        <input type="checkbox" name="botcheck" class="hidden" />

        <!-- Name field -->
        <div class="mb-4">
          <label for="name" class="block font-semibold text-sm text-gray-700 mb-1">Your Name *</label>
          <input type="text" id="name" name="name" required
            class="w-full border border-gray-300 rounded-lg px-4 py-3 text-sm focus:outline-none focus:ring-2 focus:ring-scout-blue" />
        </div>

        <!-- Email field -->
        <div class="mb-4">
          <label for="email" class="block font-semibold text-sm text-gray-700 mb-1">Email Address *</label>
          <input type="email" id="email" name="email" required
            class="w-full border border-gray-300 rounded-lg px-4 py-3 text-sm focus:outline-none focus:ring-2 focus:ring-scout-blue" />
        </div>

        <!-- Child's age field (per CONT-01 requirement) -->
        <div class="mb-4">
          <label for="child_age" class="block font-semibold text-sm text-gray-700 mb-1">Child's Age *</label>
          <input type="number" id="child_age" name="child_age" min="4" max="25" required
            class="w-full border border-gray-300 rounded-lg px-4 py-3 text-sm focus:outline-none focus:ring-2 focus:ring-scout-blue" />
        </div>

        <!-- Optional message -->
        <div class="mb-6">
          <label for="message" class="block font-semibold text-sm text-gray-700 mb-1">Message (optional)</label>
          <textarea id="message" name="message" rows="4"
            class="w-full border border-gray-300 rounded-lg px-4 py-3 text-sm focus:outline-none focus:ring-2 focus:ring-scout-blue"></textarea>
        </div>

        <!-- hCaptcha — zero-config via Web3Forms script (CONT-03) -->
        <!-- Must enable hCaptcha in Web3Forms dashboard for this form's access key -->
        <div class="h-captcha" data-captcha="true"></div>

        <button type="submit" id="submit-btn"
          class="mt-6 bg-scout-blue text-white font-display font-bold px-8 py-4 rounded-full hover:bg-scout-blue-dark transition-colors w-full">
          Send Inquiry
        </button>
      </form>
    </div>
  </section>
</BaseLayout>

<!-- Web3Forms + hCaptcha script — must be before </body> -->
<script src="https://web3forms.com/client/script.js" async defer></script>

<script>
  // Source: https://docs.web3forms.com/how-to-guides/static-site-generators/astro
  const form = document.getElementById('contact-form') as HTMLFormElement;
  const result = document.getElementById('result') as HTMLDivElement;
  const submitBtn = document.getElementById('submit-btn') as HTMLButtonElement;

  form?.addEventListener('submit', async (e) => {
    e.preventDefault();
    submitBtn.disabled = true;
    submitBtn.textContent = 'Sending...';
    result.className = 'mb-4 p-4 rounded-lg text-sm font-semibold';
    result.textContent = 'Please wait...';
    result.classList.remove('hidden');

    const formData = new FormData(form);
    const object = Object.fromEntries(formData.entries());
    const json = JSON.stringify(object);

    try {
      const response = await fetch('https://api.web3forms.com/submit', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
        body: json,
      });
      const data = await response.json();

      if (data.success) {
        result.classList.add('bg-green-100', 'text-green-800');
        result.textContent = 'Thank you! We\'ll be in touch soon.';
        form.reset();
      } else {
        result.classList.add('bg-red-100', 'text-red-800');
        result.textContent = data.message ?? 'Something went wrong. Please try again.';
      }
    } catch {
      result.classList.add('bg-red-100', 'text-red-800');
      result.textContent = 'Network error. Please try again.';
    } finally {
      submitBtn.disabled = false;
      submitBtn.textContent = 'Send Inquiry';
      setTimeout(() => result.classList.add('hidden'), 6000);
    }
  });
</script>
```

**Web3Forms hCaptcha enablement:** After creating the access key at web3forms.com, log in to the dashboard, select the form, and enable hCaptcha as the captcha provider. The `<div class="h-captcha" data-captcha="true">` div and the `<script src="https://web3forms.com/client/script.js">` tag do the rest. No separate hCaptcha account or site key needed on the free plan.

### Pattern 5: Events / Term Program Page

**What:** A static events page with term data hardcoded as an array in the Astro frontmatter. Structured for future CMS migration in Phase 4 (the data shape should match what Sveltia CMS markdown files will provide).

**When to use:** Phase 2 needs at least one real term entry (EVNT-01). Data lives in the page until Phase 4 migrates it to content collections.

```astro
---
// src/pages/events.astro
import BaseLayout from '../layouts/BaseLayout.astro';

// Phase 4 will replace this with Astro content collections from src/content/events/
// Keep the data shape consistent so migration is mechanical
const termProgram = [
  {
    term: 'Term 1 2026',
    dates: 'January 28 – March 28, 2026',
    entries: [
      { date: 'Feb 5', activity: 'Camp Cooroy — overnight bushcraft camp', section: 'all' },
      { date: 'Feb 12', activity: 'Navigation challenge at Cooroy State Forest', section: 'scouts' },
      { date: 'Mar 5', activity: 'Community service day — Cooroy town clean-up', section: 'all' },
    ],
  },
];
---

<BaseLayout title="Events & Term Program" description="...">
  ...
</BaseLayout>
```

**Data placeholder note:** The actual dates and activities must come from the group leader before this page goes live. The structure above is illustrative. EVNT-01 success criterion requires "at least one real term entry — not placeholder content."

### Anti-Patterns to Avoid

- **Using `[section].astro` dynamic routing for the 4 section pages:** Dynamic routing with `getStaticPaths()` adds complexity for only 4 routes with completely different content. Individual files are cleaner.
- **Mixing old and new brand tokens without updating Nav/Footer:** If `scout-green` tokens are removed from `@theme` but Nav still uses `bg-scout-green`, the build will succeed (Tailwind silently drops missing classes) but the nav will lose its background color. Always grep for old class usage before removing tokens.
- **Keeping both old and new color tokens indefinitely:** Define a clear point (end of Phase 2) to remove `scout-green` and `scout-gold` tokens. Keeping both indefinitely causes confusion for future editors.
- **Putting form API key in git tracked files without .env:** The Web3Forms access key is technically public (it IS in the HTML), but for clarity and future CMS editing, consider a `.env` variable `PUBLIC_WEB3FORMS_KEY`. However, since Astro static builds include it in HTML anyway, a hardcoded value in the `.astro` file is acceptable for this project scale.
- **Server-side Turnstile verification:** Cloudflare Turnstile requires server-side token validation, making it incompatible with a fully static Astro site without a Cloudflare Worker. Web3Forms hCaptcha (zero-config, free plan) achieves CONT-03 without this complexity.
- **Inline `style` attributes for section colors:** Always use `@theme` tokens so the planner/Tailwind CLI can purge correctly. Never `style="background-color: #C8956C"`.

---

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Email form backend | Custom server endpoint or Netlify Functions | Web3Forms API | Zero backend, free tier is sufficient, email forwarding handled |
| CAPTCHA / spam protection | Custom bot detection logic | Web3Forms hCaptcha (zero-config) | Complex ML-based detection; Web3Forms provides validated implementation |
| Form validation | Custom JS validation library | Native HTML5 `required`, `type="email"`, `min`/`max` attributes | Browser-native validation handles 90% of cases; no library needed |
| Section color themes | CSS-in-JS or inline styles | Tailwind v4 `@theme` section color tokens | Consistent purging, utility class API, design system coherence |
| Age progression stepper | Third-party UI library | Custom `SectionProgression.astro` component | Simple enough (4 items, static) to build in ~20 lines; no library adds value |
| Content CMS for events | Direct file editing or full CMS in Phase 2 | Hardcoded array → migrate to Sveltia CMS in Phase 4 | CMS requires live deployment URL for OAuth; not available in Phase 2 |

**Key insight:** Phase 2 is a content phase, not an infrastructure phase. The stack is fixed from Phase 1; the work is writing pages, writing content, and wiring up the form.

---

## Common Pitfalls

### Pitfall 1: Updating Color Tokens Without Finding All Usages

**What goes wrong:** `scout-green` replaced with `scout-blue` in `@theme` but `Nav.astro`, `Footer.astro`, and `index.astro` still use `bg-scout-green`, `text-scout-green`, etc. Tailwind silently outputs nothing for unknown token names — the nav background disappears.

**Why it happens:** Tailwind v4 purges classes it doesn't recognise from the token namespace silently. No build error is thrown.

**How to avoid:** Before removing any `@theme` token, grep the project for all usages:
```bash
grep -r "scout-green\|scout-gold" src/
```
Replace all occurrences before the token is removed. The safer approach: add new tokens (`scout-blue`, `scout-navy`) first, update all usages, then remove the old tokens in a separate commit.

**Warning signs:** Nav header has no background color in browser preview.

### Pitfall 2: hCaptcha Not Appearing (Web3Forms Dashboard Not Configured)

**What goes wrong:** The `<div class="h-captcha" data-captcha="true">` and Web3Forms script are in the HTML, but hCaptcha widget does not render and form submits without challenge.

**Why it happens:** Web3Forms hCaptcha must be ENABLED per-form in the Web3Forms dashboard. Adding the HTML div alone is not sufficient — the server-side must also know to require hCaptcha validation.

**How to avoid:** After getting the access key, log in to `app.web3forms.com`, find the form by access key, and toggle "Enable hCaptcha" in the spam settings. Verify the widget renders in browser before considering CONT-03 complete.

**Warning signs:** Form submits instantly without showing any captcha widget. No visual hCaptcha checkbox appears.

### Pitfall 3: Form Accessible Only at `/join` But Nav Still Links to `/contact`

**What goes wrong:** The existing Nav.astro does not have a link to `/join` or `/contact` — it only has `/sections`, `/about`, `/events`, `/gallery`. The "Join Us" button in the nav points to `/join` (confirmed in Nav.astro). If the contact page is created at `/contact` instead of `/join`, the nav CTA breaks.

**Why it happens:** The Phase 1 nav was built with `/join` as the path for the CTA button. The contact page must be at `src/pages/join.astro` to match.

**How to avoid:** Create the file at `src/pages/join.astro` (not `/contact`). The existing nav "Join Us" button already links to `/join`.

**Warning signs:** Clicking "Join Us" in nav returns a 404.

### Pitfall 4: Section Pages Not Found (Wrong Path)

**What goes wrong:** Section pages created at `src/pages/joeys.astro` instead of `src/pages/sections/joeys.astro`. The index.astro section cards link to `/sections` (not `/joeys`).

**Why it happens:** The existing `index.astro` has a "Learn more →" link that goes to `/sections` (not individual section pages yet). Individual links should resolve to `/sections/joeys` etc.

**How to avoid:** Create section subdirectory: `src/pages/sections/`. The Nav already has `/sections` as a link. Section card links on the homepage update to `/sections/joeys`, `/sections/cubs`, etc.

**Warning signs:** Section page links return 404.

### Pitfall 5: Events Page Has Only Placeholder Content

**What goes wrong:** Phase 2 is considered complete with lorem ipsum in the events page. EVNT-01 success criterion explicitly requires "at least one real term entry — not placeholder content."

**Why it happens:** Group leader may not have provided term dates yet.

**How to avoid:** This is a content dependency, not a code dependency. EVNT-01 can only be signed off once the group leader provides at least one real term entry. Make the page structure correct but flag it as blocked pending leader input if real data is unavailable.

**Warning signs:** Phase verification fails with "events page shows placeholder content."

### Pitfall 6: Web3Forms Access Key Not Obtained Before Contact Form Work

**What goes wrong:** The contact form is built and working in development, but the access key field contains `YOUR_ACCESS_KEY_HERE`. Form submissions during testing go nowhere.

**Why it happens:** The Web3Forms access key requires an email address to register — this must come from the group leader or whoever owns the group inbox.

**How to avoid:** Obtain the access key early in Phase 2 execution. The access key signup is free at `https://web3forms.com/#start`. Until the real key is available, test with a temporary personal email key.

**Warning signs:** Form shows "Success!" but no email arrives in inbox. (Or `data.success` is `false` with a key-related error.)

---

## Code Examples

Verified patterns from official sources:

### Web3Forms Minimal Form Structure

```html
<!-- Source: https://docs.web3forms.com/getting-started/customizations/spam-protection/hcaptcha -->
<form action="https://api.web3forms.com/submit" method="POST">
    <input type="hidden" name="access_key" value="YOUR_ACCESS_KEY_HERE">
    <input type="hidden" name="subject" value="New Contact Form Submission">
    <input type="checkbox" name="botcheck" class="hidden" style="display:none">

    <!-- Your form fields here -->
    <input type="text" name="name" required>
    <input type="email" name="email" required>

    <!-- hCaptcha widget -->
    <div class="h-captcha" data-captcha="true"></div>

    <button type="submit">Submit</button>
</form>

<!-- Web3Forms client script (handles hCaptcha + submission flow) -->
<script src="https://web3forms.com/client/script.js" async defer></script>
```

### Astro File-Based Routing for Nested Pages

```
// Source: https://docs.astro.build/en/basics/astro-pages/
// File at: src/pages/sections/joeys.astro
// Resolves to URL: /sections/joeys (automatic — no config needed)
```

### Tailwind v4 @theme Section Color Tokens

```css
/* Source: https://tailwindcss.com/docs/theme */
@theme {
  /* Section tokens generate: bg-section-joeys, text-section-joeys, etc. */
  --color-section-joeys:    #C8956C;
  --color-section-cubs:     #F5C400;
  --color-section-scouts:   #077840;
  --color-section-venturers: #8B1A2E;
}
```

### Web3Forms JavaScript Fetch Handler (Astro-safe)

```typescript
// For Astro: this script must go in a <script> block at the bottom of the .astro file
// NOT in the frontmatter (---) block
// Source: https://docs.web3forms.com/how-to-guides/static-site-generators/astro

const form = document.getElementById('contact-form') as HTMLFormElement;

form?.addEventListener('submit', async (e) => {
  e.preventDefault();
  const formData = new FormData(form);
  const object = Object.fromEntries(formData.entries());

  const response = await fetch('https://api.web3forms.com/submit', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json', 'Accept': 'application/json' },
    body: JSON.stringify(object),
  });

  const data = await response.json();
  if (data.success) {
    // Show success message, reset form
    form.reset();
  }
});
```

---

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| scout-green (#077840) as primary | scout-blue (#2EA3F2) as primary | Phase 2 (user decision) | Nav, Footer, CTAs all use blue; green demoted to Scouts section only |
| All 4 sections use generic bg-yellow/bg-orange/bg-green | Section-specific @theme tokens | Phase 2 | Consistent token API; section identity matches real uniform colors |
| `/join` links existed but page was placeholder | Real Web3Forms form | Phase 2 | CONT-01/02/03 fulfilled |
| index.astro had generic placeholder cards | Real content, correct section colors, live links | Phase 2 | HOME-01/02 fulfilled |

**Deprecated/outdated after Phase 2:**
- `--color-scout-green` and `--color-scout-gold` as PRIMARY brand colors: These become SECTION-SCOUTS only (`--color-section-scouts`) after Phase 2. Do not use them as site-wide brand colors in Phase 3+.

---

## Open Questions

1. **Section color hex values — no official source found**
   - What we know: Section identity colors are tan (Joeys), yellow (Cubs), green (Scouts), maroon (Venturers) per Scouts Australia uniform standards (confirmed via multiple QLD group uniform pages and Wikipedia). No official hex codes exist in any public brand document.
   - What's unclear: The exact shade of tan for Joeys (could be lighter/darker) and the exact maroon for Venturers.
   - Recommendation: Use the working approximations (`#C8956C` tan, `#8B1A2E` maroon) and visually verify against a physical Joey/Venturer uniform or ask brandsupport@scoutsqld.com.au. These are low-risk approximations — the exact hex matters far less than picking a clearly identifiable tan vs maroon.

2. **Scout-navy (#212C66) — user-provided, not independently verified**
   - What we know: The user states they visually reviewed scoutsqld.com.au and identified #212C66 as the accent navy. Primary blue #2EA3F2 was independently confirmed via CSS inspection.
   - What's unclear: Whether #212C66 is the official Scouts QLD navy or a generic dark navy from a theme.
   - Recommendation: Accept user's assessment for Phase 2. If it looks wrong, darkening or lightening the navy value in `@theme` is a one-line change.

3. **Real meeting times and location — must come from group leader**
   - What we know: STATE.md documents this as a known blocker: "Real meeting times and location required from group leader before section pages can be completed."
   - What's unclear: Whether the old website (Wayback Machine) has reliable times, or whether they have changed.
   - Recommendation: Flag section pages as content-blocked if group leader hasn't provided times. Build the page structure with `[Meeting day: TBD]` placeholders, but do not mark SECT-01 through SECT-04 as complete until real data is present.

4. **Web3Forms access key — must be obtained from group leader's email**
   - What we know: The access key is tied to the email address that will receive form submissions. It must belong to the group email inbox owner.
   - Recommendation: Group leader provides email address; researcher or developer registers at web3forms.com/#start. Key is returned immediately by email.

5. **Contact page title: "Join Us" vs "Contact"**
   - The page at `/join` (per Nav.astro's CTA) will contain both a contact inquiry form AND joining instructions. The page should be titled "Join Us" (matches nav CTA) but the `<h1>` can clarify it contains a contact form. This avoids confusion with the nav link label.

---

## Validation Architecture

### Test Framework

| Property | Value |
|----------|-------|
| Framework | None installed — visual/manual validation per Phase 1 precedent |
| Config file | None |
| Quick run command | `npm run build` — confirms no TypeScript/Astro errors |
| Full suite command | `npm run build && npm run preview` — live preview for visual inspection |

### Phase Requirements → Test Map

| Req ID | Behavior | Test Type | Automated Command | File Exists? |
|--------|----------|-----------|-------------------|-------------|
| HOME-01 | Hero visible above fold on mobile with working "Join Us" CTA | Manual visual | `npm run dev` + DevTools mobile viewport | Wave 0: create join.astro |
| HOME-02 | Section cards on homepage link to individual section pages | Manual navigation | `npm run preview` + click each card link | Wave 0: create section pages |
| ABUT-01 | About page renders at /about with group content | Build smoke | `npm run build` (exit 0 = page exists) | Wave 0: create about.astro |
| CONT-01 | Contact form has name, child's age, email fields | Manual visual | `npm run dev` + inspect form fields | Wave 0: create join.astro |
| CONT-02 | Form submission arrives in group email inbox | Manual integration | Submit test form, check inbox | Manual only — requires Web3Forms key + real email |
| CONT-03 | hCaptcha widget renders and blocks submission without completion | Manual visual | `npm run dev` + attempt form submission without captcha | Manual only — requires hCaptcha dashboard activation |
| SECT-01 | Joeys page shows age 5-7, meeting time/location, description | Manual visual | `npm run preview` + navigate to /sections/joeys | Wave 0: create joeys.astro |
| SECT-02 | Cubs page shows age 8-10, meeting time/location, description | Manual visual | `npm run preview` + navigate to /sections/cubs | Wave 0: create cubs.astro |
| SECT-03 | Scouts page shows age 11-14, meeting time/location, description | Manual visual | `npm run preview` + navigate to /sections/scouts | Wave 0: create scouts.astro |
| SECT-04 | Venturers page shows age 15-17, meeting time/location, description | Manual visual | `npm run preview` + navigate to /sections/venturers | Wave 0: create venturers.astro |
| SECT-05 | Each section page shows activity previews | Manual visual | `npm run preview` + check each section page | Included in section page files |
| SECT-06 | Age progression pathway visible on section pages | Manual visual | `npm run preview` + check SectionProgression component | Wave 0: create SectionProgression.astro |
| EVNT-01 | Events page shows at least one real term entry | Content review | `npm run preview` + review events page content | Wave 0: create events.astro; content TBD |

### Sampling Rate

- **Per task commit:** `npm run build` — confirms no TypeScript/Astro compilation errors
- **Per wave merge:** `npm run build` + `npm run preview` + manual browser navigation to all new routes
- **Phase gate:** All success criteria verified manually; real form submission tested; no broken links

### Wave 0 Gaps

- [ ] `src/pages/join.astro` — covers CONT-01, CONT-02, CONT-03
- [ ] `src/pages/about.astro` — covers ABUT-01
- [ ] `src/pages/events.astro` — covers EVNT-01
- [ ] `src/pages/sections/index.astro` — covers SECT-06 (sections overview)
- [ ] `src/pages/sections/joeys.astro` — covers SECT-01, SECT-05
- [ ] `src/pages/sections/cubs.astro` — covers SECT-02, SECT-05
- [ ] `src/pages/sections/scouts.astro` — covers SECT-03, SECT-05
- [ ] `src/pages/sections/venturers.astro` — covers SECT-04, SECT-05
- [ ] `src/components/SectionProgression.astro` — covers SECT-06 (shared component)
- [ ] Brand token update: `src/styles/global.css` @theme — scout-blue, scout-navy, section colors

*(No automated test framework needed. Phase 2 validation is entirely visual/manual + build smoke tests.)*

---

## Sources

### Primary (HIGH confidence)
- [Astro Docs — Pages & file routing](https://docs.astro.build/en/basics/astro-pages/) — file-based routing, nested directories, static page creation
- [Astro Docs — Routing reference](https://docs.astro.build/en/guides/routing/) — `getStaticPaths` pattern, individual vs dynamic routes
- [Tailwind CSS Docs — Theme variables](https://tailwindcss.com/docs/theme) — `@theme` custom token syntax, utility class generation
- [Web3Forms hCaptcha Docs](https://docs.web3forms.com/getting-started/customizations/spam-protection/hcaptcha) — zero-config hCaptcha integration for free plan
- [Web3Forms Astro Guide](https://docs.web3forms.com/how-to-guides/static-site-generators/astro) — fetch API pattern for Astro static form
- scoutsqld.com.au CSS inspection — confirmed `#2EA3F2` as primary brand blue

### Secondary (MEDIUM confidence)
- Scouts Australia uniform standards (multiple QLD group uniform pages, Wikipedia) — section colors: tan/Joeys, yellow/Cubs, green/Scouts, maroon/Venturers confirmed
- [Rutherford.dev Web3Forms Astro snippet](https://rutherford.dev/snippets/contact-forms-in-astro_with_web3forms) — Astro-specific form implementation pattern
- Web3Forms pricing — free tier confirmed at 250 submissions/month, hCaptcha on free plan

### Tertiary (LOW confidence — flag for validation)
- Section color hex approximations: `#C8956C` (Joey tan) and `#8B1A2E` (Venturer maroon) are reasonable approximations with no official source — visually verify before final commit
- `#212C66` as scout-navy: user-provided from visual inspection, not independently verified against official brand document

---

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH — Phase 1 stack unchanged; no new npm packages
- Astro page routing: HIGH — Verified against official Astro docs
- Web3Forms integration: HIGH — Verified against official Web3Forms docs; Astro-specific pattern confirmed
- hCaptcha spam protection: HIGH — Confirmed free plan, zero-config, dashboard-activation required
- Brand token update (blue/navy): MEDIUM — #2EA3F2 confirmed via scoutsqld.com.au CSS inspection; #212C66 user-provided
- Section colors: LOW-MEDIUM — Uniform colors confirmed by name (tan/yellow/green/maroon); hex approximations not from official source
- Architecture patterns: HIGH — Standard Astro file routing; confirmed working in Phase 1

**Research date:** 2026-03-05
**Valid until:** 2026-06-05 (stable — Web3Forms API and Astro routing patterns are stable; brand colors stable unless Scouts Australia rebrands)
