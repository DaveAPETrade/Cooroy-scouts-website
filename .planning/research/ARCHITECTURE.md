# Architecture Research

**Domain:** Static informational website with git-based CMS (Astro + Decap CMS + Cloudflare Pages)
**Researched:** 2026-03-05
**Confidence:** HIGH — based on official Astro docs, Decap CMS docs, and verified community sources

## Standard Architecture

### System Overview

```
┌──────────────────────────────────────────────────────────────────┐
│                        EDITOR LAYER                              │
│  ┌─────────────────────────────────────────────────────────┐     │
│  │  Decap CMS Admin UI  (/admin)                           │     │
│  │  Browser-based editor → commits markdown to git repo    │     │
│  └─────────────────────────────────────────────────────────┘     │
└──────────────────────────┬───────────────────────────────────────┘
                           │ Git push (markdown files)
┌──────────────────────────▼───────────────────────────────────────┐
│                        SOURCE LAYER                              │
│  ┌──────────────┐  ┌─────────────────┐  ┌──────────────────┐    │
│  │  src/content │  │   src/pages/    │  │  src/components/ │    │
│  │  (markdown)  │  │  (routes/pages) │  │  (UI building    │    │
│  │              │  │                 │  │   blocks)        │    │
│  └──────┬───────┘  └────────┬────────┘  └──────────────────┘    │
│         │                   │                                    │
│  ┌──────▼───────────────────▼────────────────────────────────┐   │
│  │             content.config.ts (Zod schemas)               │   │
│  └────────────────────────┬──────────────────────────────────┘   │
└───────────────────────────┼──────────────────────────────────────┘
                            │ Astro build (getCollection / getEntry)
┌───────────────────────────▼──────────────────────────────────────┐
│                        BUILD LAYER                               │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │  Astro Static Build                                      │    │
│  │  - Processes markdown → HTML                             │    │
│  │  - Optimizes images → WebP/AVIF                          │    │
│  │  - Bundles CSS (Tailwind purge)                          │    │
│  └──────────────────────────────────────────────────────────┘    │
└───────────────────────────┬──────────────────────────────────────┘
                            │ dist/ (static HTML/CSS/JS/images)
┌───────────────────────────▼──────────────────────────────────────┐
│                        DELIVERY LAYER                            │
│  ┌──────────────────────────────────────────────────────────┐    │
│  │  Cloudflare Pages CDN                                    │    │
│  │  - Serves static files globally                          │    │
│  │  - GitHub CI/CD trigger on main branch push              │    │
│  └──────────────────────────────────────────────────────────┘    │
│                                                                  │
│  ┌───────────────────┐  ┌─────────────────────────────────────┐  │
│  │  Formspree        │  │  GitHub OAuth (Cloudflare Functions)│  │
│  │  (form handling)  │  │  (Decap CMS authentication)         │  │
│  └───────────────────┘  └─────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────┘
```

### Component Responsibilities

| Component | Responsibility | Typical Implementation |
|-----------|----------------|------------------------|
| `src/content.config.ts` | Defines all content collections with Zod schemas | `defineCollection()` + `glob()` loader + `z.object()` schema |
| `src/content/` | Raw markdown/YAML data files edited by CMS | `.md` files with frontmatter matching the Zod schema |
| `src/pages/` | File-based routing — each file = one URL path | `.astro` files, static or using `getStaticPaths()` |
| `src/layouts/` | Shared page shell (html/head/body, nav, footer) | `BaseLayout.astro` with `<slot />` for page content |
| `src/components/` | Reusable UI building blocks | `.astro` components (no client JS unless Islands needed) |
| `public/admin/` | Decap CMS admin interface configuration | `config.yml` defining collection fields for the CMS editor |
| `public/` | Static assets not needing processing | Fonts, favicon, PDFs, robot.txt |
| `src/images/` | Images that Astro should optimise at build time | `.jpg`, `.png` files referenced via `<Image />` component |

## Recommended Project Structure

```
/
├── public/
│   ├── admin/
│   │   └── config.yml          # Decap CMS collection schema (mirrors content.config.ts)
│   ├── favicon.svg
│   └── robots.txt
│
├── src/
│   ├── content/
│   │   ├── sections/           # Joey, Cubs, Scouts, Venturers — one .md per section
│   │   └── events/             # Term program entries — updated 4x/year via CMS
│   │
│   ├── content.config.ts       # Zod schemas for all collections
│   │
│   ├── pages/
│   │   ├── index.astro         # Home (hero, CTA)
│   │   ├── about.astro         # About/history (static content)
│   │   ├── sections/
│   │   │   └── [slug].astro    # Dynamic route from sections collection
│   │   ├── events.astro        # Term program (queries events collection)
│   │   ├── gallery.astro       # Photo gallery (queries gallery images or static)
│   │   ├── contact.astro       # Contact/Join Us (Formspree form)
│   │   └── admin.astro         # Decap CMS admin entry point
│   │
│   ├── layouts/
│   │   └── BaseLayout.astro    # Site shell: <html>, <head>, nav, footer, <slot />
│   │
│   ├── components/
│   │   ├── Nav.astro           # Navigation bar
│   │   ├── Footer.astro        # Footer with contact/links
│   │   ├── Hero.astro          # Home page hero section
│   │   ├── SectionCard.astro   # Card for each scout section
│   │   ├── EventCard.astro     # Individual event/term program entry
│   │   ├── GalleryGrid.astro   # Photo grid layout
│   │   └── ContactForm.astro   # Formspree-backed form
│   │
│   ├── images/                 # Processed by Astro <Image /> — WebP output
│   │   ├── gallery/            # Gallery photos
│   │   └── sections/           # Section-specific photos
│   │
│   └── styles/
│       └── global.css          # Tailwind directives + any custom CSS variables
│
├── astro.config.mjs
├── tailwind.config.mjs
├── tsconfig.json
└── package.json
```

### Structure Rationale

- **`src/content/`:** Markdown files that non-technical editors update via Decap CMS. Keep separate from pages so the CMS configuration maps cleanly to these directories.
- **`src/pages/`:** Owns routing. Static pages (home, about, contact) are plain `.astro` files. Dynamic section pages use `[slug].astro` with `getStaticPaths()` pulling from the content collection.
- **`src/layouts/`:** A single `BaseLayout.astro` keeps `<head>` meta, nav, and footer in one place. All pages pass through it — this is how site-wide changes (branding, nav updates) propagate everywhere.
- **`src/components/`:** Each UI concern is isolated. This is where Tailwind classes live, keeping pages and layouts clean.
- **`public/admin/`:** Decap CMS reads `config.yml` from here at runtime. The schema here must mirror `content.config.ts` — they describe the same data in two different formats.
- **`src/images/`:** Keeping images in `src/` (not `public/`) means Astro processes them through the `<Image />` component, generating optimised WebP/AVIF at build time.

## Architectural Patterns

### Pattern 1: Content Collection + Dynamic Route

**What:** Define a Zod-validated collection in `content.config.ts`, then generate one static page per entry using `getStaticPaths()`.

**When to use:** Any content type with repeating structure — scout sections, events, gallery albums.

**Trade-offs:** Adds a build step per entry (fine at this scale — 4-10 entries). Strongly typed — CMS mistakes surface at build time, not at runtime.

**Example:**
```typescript
// src/content.config.ts
import { z, defineCollection } from 'astro:content';
import { glob } from 'astro/loaders';

const sections = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/sections' }),
  schema: z.object({
    title: z.string(),           // e.g. "Joeys"
    ageRange: z.string(),        // e.g. "5-7 years"
    meetingDay: z.string(),
    meetingTime: z.string(),
    description: z.string(),
    heroImage: z.string().optional(),
  }),
});

const events = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/events' }),
  schema: z.object({
    title: z.string(),
    term: z.string(),            // e.g. "Term 2, 2026"
    startDate: z.date(),
    endDate: z.date(),
    activities: z.array(z.string()),
  }),
});

export const collections = { sections, events };
```

```typescript
// src/pages/sections/[slug].astro
---
import { getCollection } from 'astro:content';
import BaseLayout from '../../layouts/BaseLayout.astro';

export async function getStaticPaths() {
  const sections = await getCollection('sections');
  return sections.map(section => ({
    params: { slug: section.id },
    props: { section },
  }));
}

const { section } = Astro.props;
const { Content } = await section.render();
---
<BaseLayout title={section.data.title}>
  <h1>{section.data.title}</h1>
  <p>{section.data.ageRange}</p>
  <Content />
</BaseLayout>
```

### Pattern 2: BaseLayout with Named Slots

**What:** A single `BaseLayout.astro` renders the full HTML shell. Pages inject content into `<slot />`. Named slots allow pages to inject additional `<head>` tags (for per-page SEO).

**When to use:** Every page in the site. This is non-negotiable for a multi-page site — without it, nav/footer changes require editing every page file.

**Trade-offs:** None significant at this scale. The only complexity is remembering to pass `title` and optional `description` props.

**Example:**
```typescript
// src/layouts/BaseLayout.astro
---
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
    <meta name="viewport" content="width=device-width" />
    <title>{title} | Cooroy Scouts</title>
    <meta name="description" content={description} />
    <slot name="head" />   <!-- pages can inject extra <meta> or <link> tags -->
  </head>
  <body>
    <Nav />
    <main>
      <slot />             <!-- primary page content goes here -->
    </main>
    <Footer />
  </body>
</html>
```

### Pattern 3: Decap CMS Admin Route (Minimal SSR Island)

**What:** Serve the Decap CMS admin at `/admin` by creating `src/pages/admin.astro`. This page loads the Decap CMS bundle from CDN or npm. The rest of the site stays fully static.

**When to use:** Whenever a non-technical editor needs a browser-based editing UI. The admin page is the only "dynamic" element — everything else is static HTML.

**Trade-offs:** Requires GitHub OAuth setup via Cloudflare Functions (or a third-party Cloudflare Worker proxy). OAuth is the main complexity in this architecture. Local development requires running `npx decap-server` alongside the Astro dev server.

**Example (CDN approach — simplest):**
```html
<!-- src/pages/admin.astro -->
---
// No server-side code needed — Decap CMS is pure client-side React
---
<!doctype html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Cooroy Scouts — Content Manager</title>
  </head>
  <body>
    <!-- Decap CMS mounts here -->
    <script src="https://unpkg.com/decap-cms@^3.0.0/dist/decap-cms.js"></script>
  </body>
</html>
```

```yaml
# public/admin/config.yml
backend:
  name: github
  repo: your-org/cooroy-scouts-website
  branch: main
  base_url: https://cooroy-scouts.pages.dev   # points to Cloudflare Functions OAuth handler
  auth_endpoint: /oauth

media_folder: src/images/gallery
public_folder: /images/gallery

collections:
  - name: sections
    label: Scout Sections
    folder: src/content/sections
    create: false     # sections are fixed, don't allow new entries
    fields:
      - { label: Title, name: title, widget: string }
      - { label: Age Range, name: ageRange, widget: string }
      - { label: Meeting Day, name: meetingDay, widget: string }
      - { label: Meeting Time, name: meetingTime, widget: string }
      - { label: Description, name: description, widget: markdown }

  - name: events
    label: Term Programs
    folder: src/content/events
    create: true      # volunteers add new term programs here
    fields:
      - { label: Title, name: title, widget: string }
      - { label: Term, name: term, widget: string }
      - { label: Start Date, name: startDate, widget: datetime }
      - { label: End Date, name: endDate, widget: datetime }
      - { label: Activities, name: activities, widget: list }
```

## Data Flow

### Build-Time Content Flow

```
Volunteer edits via Decap CMS UI
    ↓
Decap commits markdown file to GitHub (src/content/events/*.md)
    ↓
Cloudflare Pages detects push → triggers build
    ↓
Astro build runs:
  getCollection('events') reads all .md files
  Zod validates frontmatter against schema
  getStaticPaths() maps entries to URLs
  Each page rendered to static HTML
    ↓
dist/ output deployed to Cloudflare CDN
    ↓
Visitor loads static HTML — zero server round-trips
```

### Form Submission Flow

```
Visitor fills out contact form on /contact
    ↓
Browser submits POST to Formspree endpoint (action="https://formspree.io/f/[id]")
    ↓
Formspree validates and forwards email to configured recipient (e.g. group leader)
    ↓
Formspree redirects visitor to thank-you page (or shows inline message)
    ↓
No data stored on Cooroy Scouts infrastructure
```

### Image Processing Flow

```
Image file added to src/images/ (by developer) or src/images/gallery/ (via Decap CMS upload)
    ↓
Astro <Image /> component references the file
    ↓
Build: Astro processes image → generates WebP (primary) + fallback formats
    ↓
Outputs responsive srcset with correct width/height (prevents layout shift)
    ↓
HTML references optimised image URL in dist/
```

### Key Data Flows Summary

1. **Content update:** Volunteer edits in Decap CMS browser UI → GitHub commit → Cloudflare Pages rebuild → live within ~60 seconds
2. **Page load:** Visitor requests URL → Cloudflare serves static HTML from CDN edge → zero origin requests
3. **Form submission:** Visitor submits form → Formspree handles → email delivered → no backend on site
4. **CMS authentication:** Volunteer opens /admin → Decap redirects to GitHub OAuth → Cloudflare Functions handles callback → session established in browser

## Scaling Considerations

This is a small community site. Scaling is not a concern; the architecture is chosen to be maintainable and zero-ops.

| Scale | Architecture |
|-------|-------------|
| Current (handful of editors, ~100-500 page views/month) | Fully static, Cloudflare Pages free tier — no action needed |
| 10x growth | Still fully static. Cloudflare free tier handles millions of requests. No changes. |
| Major scope expansion (member login, events booking) | Would require server-side rendering — Astro SSR mode + Cloudflare Workers. Not in scope. |

### Scaling Priorities

1. **First bottleneck:** Build times if gallery grows to hundreds of images — mitigated by Astro's content layer caching (images cached between builds).
2. **Second bottleneck:** There is none at this scale.

## Anti-Patterns

### Anti-Pattern 1: Putting CMS Images in `public/` Instead of `src/images/`

**What people do:** Configure Decap CMS `media_folder` to point at `public/images/`.

**Why it's wrong:** Files in `public/` bypass Astro's image processing pipeline. They are served as-is — full-size JPEGs, no WebP conversion, no responsive srcset, no automatic width/height attributes. Gallery page performance degrades significantly.

**Do this instead:** Set `media_folder: src/images/gallery` and `public_folder: /images/gallery` in Decap CMS config. Reference uploaded images via Astro's `<Image />` component so they are optimised at build time.

### Anti-Pattern 2: Duplicating Schema Between `content.config.ts` and `public/admin/config.yml`

**What people do:** Define fields in one place and forget to update the other when content structure changes.

**Why it's wrong:** The Zod schema and the Decap CMS config are two representations of the same data model. If they diverge, the CMS editor either shows irrelevant fields or fails to create valid frontmatter, breaking the build.

**Do this instead:** Treat both files as a coupled pair. When you add a field to `content.config.ts`, immediately add the matching widget to `config.yml`. Comment each field with its counterpart location.

### Anti-Pattern 3: Skipping `BaseLayout` and Copying HTML Shell Into Every Page

**What people do:** On simple sites, copy the `<html>`, `<head>`, nav, and footer markup into each `.astro` page file.

**Why it's wrong:** The nav requires a change → edit 6+ files. A missed file ships broken nav. This is the most common maintenance trap on small static sites.

**Do this instead:** `BaseLayout.astro` on day one, before writing any pages. All pages are `<BaseLayout title="..."><main content /></BaseLayout>`.

### Anti-Pattern 4: Using Astro Islands (Client-Side Hydration) for the Gallery Lightbox

**What people do:** Reach for a React or Vue component to add lightbox interactivity to the gallery, hydrating the entire gallery grid client-side.

**Why it's wrong:** The gallery grid is static HTML + images. Hydrating the whole component ships unnecessary JavaScript and breaks Astro's zero-JS default.

**Do this instead:** Use a lightweight vanilla JS lightbox library (e.g., GLightbox, PhotoSwipe) loaded as a script in the `<head>` or deferred. If you must use Astro Islands, scope hydration to only the lightbox trigger button, not the entire grid.

## Integration Points

### External Services

| Service | Integration Pattern | Notes |
|---------|---------------------|-------|
| Decap CMS | Static admin page at `/admin` + `public/admin/config.yml` | Requires GitHub OAuth setup. Use Cloudflare Functions or `dorukgezici/astro-decap-cms-oauth` package. Local dev requires `npx decap-server` on port 8081. |
| GitHub OAuth | Cloudflare Functions handler at `/oauth` and `/oauth/callback` | GitHub OAuth App must be registered with callback URL pointing to your Pages domain. |
| Formspree | Plain HTML `<form action="https://formspree.io/f/[id]">` | No JavaScript required for basic submission. Add JS for inline success/error messages. Free tier supports 50 submissions/month — sufficient for a scout group. |
| Cloudflare Pages | Push to `main` branch triggers build automatically | Build command: `npm run build`. Output dir: `dist`. Set via Cloudflare Pages dashboard. |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| `content.config.ts` ↔ `src/pages/` | `getCollection()` / `getEntry()` at build time | Pages are the only consumers of collections. Components never query collections directly. |
| `src/pages/` ↔ `src/layouts/` | Props passing (`title`, `description`) + `<slot />` | Layouts know nothing about page content. Pages decide what data to render. |
| `src/pages/` ↔ `src/components/` | Props passing | Components are pure — they receive props and render markup. No side effects, no data fetching. |
| Decap CMS UI ↔ `src/content/` | Git commits via GitHub API | Decap writes markdown files directly to the repo. Astro never communicates with Decap at build time — it just reads the files Decap wrote. |

## Build Order Implications

The component dependency graph dictates what to build first:

```
1. BaseLayout.astro          (required by every page — build first)
2. Nav.astro + Footer.astro  (required by BaseLayout)
3. content.config.ts         (required by any page using collections)
4. Static pages              (index, about, contact — no collection dependency)
5. Dynamic pages             (sections/[slug], events — require collection + getStaticPaths)
6. Gallery page              (requires image processing setup)
7. public/admin/config.yml   (Decap CMS — requires finalized content schema)
8. OAuth setup               (Cloudflare Functions — last because it requires a live deployment URL)
```

The CMS integration (steps 7-8) should come after all pages and content schemas are finalised. Changing the schema after the CMS is configured means updating `config.yml` too, which is extra work.

## Sources

- [Astro Content Collections — Official Docs](https://docs.astro.build/en/guides/content-collections/) — HIGH confidence
- [Astro Project Structure — Official Docs](https://docs.astro.build/en/basics/project-structure/) — HIGH confidence
- [Astro Layouts — Official Docs](https://docs.astro.build/en/basics/layouts/) — HIGH confidence
- [Decap CMS & Astro — Official Astro Docs](https://docs.astro.build/en/guides/cms/decap-cms/) — HIGH confidence
- [Astro + Decap in 2026 — DEV Community](https://dev.to/migsarnavarro/astro-decap-in-2026-3mj3) — MEDIUM confidence (community article, cross-checked with official docs)
- [astro-decap-cloudflare-starter — GitHub](https://github.com/jshuntley/astro-decap-cloudflare-starter) — MEDIUM confidence (community starter, verified approach works)
- [decap-cms-cloudflare-pages — GitHub](https://github.com/SubhenduX/decap-cms-cloudflare-pages) — MEDIUM confidence (community OAuth proxy, referenced in multiple sources)
- [Adding a Form to Astro — Formspree Docs](https://formspree.io/guides/astro/) — HIGH confidence (official Formspree documentation)
- [Astro Images — Official Docs](https://docs.astro.build/en/guides/images/) — HIGH confidence
- [Build an image gallery with Astro — Nemanja Mitic](https://nemanjamitic.com/blog/2025-04-02-astro-react-gallery/) — LOW confidence (single community source, general pattern confirmed elsewhere)

---
*Architecture research for: Astro static informational site with git-based CMS*
*Researched: 2026-03-05*
