# Feature Research

**Domain:** Scout group informational website (public-facing, static, volunteer-maintained)
**Researched:** 2026-03-05
**Confidence:** HIGH (pattern consistent across 6+ Australian scout group sites researched)

## Feature Landscape

### Table Stakes (Users Expect These)

Features users assume exist. Missing these = product feels incomplete or untrustworthy.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Hero / homepage intro with CTA | First thing a prospective parent sees — sets the tone and answers "is this right for my kid?" in seconds | LOW | Headline, one-liner mission, "Join Us" button. No wall of text. |
| Section pages per age group | Parents arrive with a specific child age in mind (5yo, 9yo, 14yo) — they need to instantly find the matching section | LOW–MEDIUM | One page each for Joeys (5–7), Cubs (8–10), Scouts (11–14), Venturers (15–17). Each must show age range, meeting day/time/location, and what activities look like. |
| Meeting time and location | The single most-asked question from prospective parents — where, when, how often | LOW | Must be on section pages AND easily findable from the homepage. Hiding this is a conversion killer. |
| Contact / Join Us page with form | Parents who can't find a way to reach the group will not join — they'll move on | LOW–MEDIUM | Simple form: name, child's age, email. Forwarded via Formspree. No backend needed. |
| About / group background | Builds trust — parents want to know this is an established, active group | LOW | Brief history, current leader names, how long the group has run. |
| Mobile-responsive design | Over 50% of web traffic is mobile; parents searching "scouts near me" are on their phone | LOW | Non-negotiable. Tailwind + Astro handles this by default with the right component approach. |
| Photo gallery | Parents use this to judge whether the group looks fun, active, and welcoming | MEDIUM | Static image grid is sufficient. No viewer needed initially. Child safety note: photos should be activity-focused, not closeups of individual children with names. |
| Term program / events page | Existing members need to know what's happening this term; prospective parents want to see the group is active | MEDIUM | Updated ~4x/year by volunteers via CMS. A simple list or calendar-style layout. |
| Scouts Queensland branding | Trust signal — parents recognize the national organization; unofficial-looking sites feel untrustworthy | LOW | Use official Scouts QLD colors (maroon or navy + white), logo, and typeface as the base. Group can choose maroon or navy as primary. |
| SSL / HTTPS | Browsers flag HTTP sites; parents won't submit a contact form to an insecure site | LOW | Cloudflare Pages provides this automatically. Zero effort required. |

### Differentiators (Competitive Advantage)

Features that set Cooroy Scouts apart from other group sites in the region.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Cooroy-specific identity within QLD branding | Groups using generic Scouts QLD templates look identical; a site with local photos, the Cooroy name and area prominently, and a genuine personality stands out | LOW | Use official brand as base but layer in local hero images (hinterland, Cooroy area), group-specific copy. This is a design/content decision, not technical. |
| Clear "first meeting is free" / low-barrier trial CTA | Many parents hesitate because they don't know the cost or commitment. Surfacing "come try it, no obligation" prominently on the homepage reduces friction dramatically | LOW | Copy-level differentiator. Single sentence + button. High conversion impact for almost zero effort. |
| Section-specific activity previews | Most group sites list age ranges but don't show what a typical night looks like. A brief "what we do" with 2–3 specific activity examples per section ("Cubs built a raft last term") builds excitement and reduces parent anxiety | MEDIUM | Requires real content from the group leader. Placeholder copy for v1, real content when leader returns. |
| Age progression / section pathway explanation | Parents with a 5yo wonder "what happens when they turn 8?" — showing the full pathway (Joeys → Cubs → Scouts → Venturers) keeps families engaged long-term | LOW | A simple visual or table on the About page or its own "Our Sections" overview. Addresses retention, not just recruitment. |
| Fee transparency | Most scout sites hide fees or require you to call to ask. Publishing approximate costs (bi-annual registration ~$150, term fees ~$80, uniform costs) builds trust and pre-qualifies enquiries | LOW | Content-only feature. Reduces "sticker shock" drop-off post-inquiry. Beenleigh Scouts does this well. |
| Volunteer involvement call-out | Groups with visible "we need helpers" messaging attract adult volunteers, not just member families. Leaders are the group's scarcest resource | LOW | Small section on the homepage or About page: "Could you help one night per week? We'd love to hear from you." |

### Anti-Features (Commonly Requested, Often Problematic)

Features that seem good but create problems for a volunteer-maintained static site.

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Member login / password-protected areas | "Private content for members only" sounds useful | Requires authentication backend — breaks the static site model entirely. Maintenance burden on volunteers who aren't developers. Security liability with children's data. | Use a separate closed Facebook Group or the Scouts Australia member portal (Scouts Member Management) for private member content. Link to it from the site. |
| Online payments / registration | "Parents should be able to pay fees on the site" | Stripe/PayPal integration requires a backend or a third-party embed with ongoing PCI compliance responsibility. Out of scope for a static informational site. | Link to the official Scouts Queensland member registration form (scoutsqld.com.au/membershipform/) for formal enrollment. |
| Real-time event calendar with RSVP | "A live calendar would be great" | Requires a backend or heavy embed (Google Calendar embeds are ugly and break on mobile). Maintaining sync between a CMS and a live calendar creates double-entry burden for volunteers. | A term program page listing activities for the current term, updated via CMS 4x/year. Simple, maintainable, fits the static model. |
| News / blog feed | "We should post updates regularly" | Volunteer groups rarely maintain a blog. Stale posts (last entry: 18 months ago) actively harm trust and make the group look inactive. | Events/term program page updated each term. Any "news" goes on the Facebook page which volunteers already use. |
| Downloadable forms / documents | "Put the membership form on the website" | Official Scouts QLD forms change — maintaining local copies creates outdated-document risk. PDFs are inaccessible on mobile. | Link directly to official Scouts QLD forms and resources. External links stay current without site maintenance. |
| Chat widget / live support | "Parents could ask questions instantly" | Nobody will monitor it. An unanswered chat widget is worse than no chat widget — it signals the group is unresponsive. | Contact form with clear "we'll respond within X days" expectation. |
| Full children's names in photo captions | "Let's label everyone in the gallery" | Digital safeguarding risk — first names + group location + photos creates potential for exploitation. Scouts Australia guidance discourages this. | Use first names only if needed, or caption with activity description ("Cubs building their raft, Term 2"). |
| Map embed showing meeting location | Seems helpful for directions | Reveals the physical address of where children gather in a public, indexed location. Combine with the safeguarding considerations above. | Provide suburb and meeting night. Send the exact address via email to confirmed enquiries only. |

## Feature Dependencies

```
Contact / Join Us form
    └──requires──> Email forwarding service (Formspree/Formspark)

Term Program page
    └──requires──> CMS integration (Tina or Decap)
                       └──requires──> Git-based deployment (Cloudflare Pages + GitHub)

Photo Gallery
    └──requires──> Image hosting / optimisation (Astro Image component or Cloudflare)

Section pages (Joeys, Cubs, Scouts, Venturers)
    └──requires──> Base page template / layout component
                       └──enhances──> Age progression pathway overview (reuses section data)

Homepage CTA ("Join Us")
    └──requires──> Contact / Join Us page exists

Scouts QLD branding
    └──enhances──> All pages (global design tokens: colors, fonts, logo)

"First meeting free" CTA
    └──enhances──> Homepage hero (copy addition, no new component)

Fee transparency
    └──enhances──> Section pages OR Contact page (content addition only)
```

### Dependency Notes

- **Term program page requires CMS:** Volunteer editors need to update this 4x/year without developer involvement. This is the primary justification for including a CMS at all. Without CMS, the term program page either never gets updated or requires a developer each time.
- **Section pages require base template:** All four section pages share the same layout (age range, meeting time, what we do, how to join). Build the template once, fill in 4x. This should be one of the first components built.
- **Photo gallery requires image handling decision:** Astro's built-in `<Image>` component handles optimisation for local images. For CMS-managed photos, a decision is needed on whether volunteers upload images via the CMS or if images are committed to git. Tina CMS supports media uploads; Decap CMS does too but with more configuration.
- **Homepage CTA requires Contact page:** Don't build the homepage without the destination page existing. These ship together.

## MVP Definition

### Launch With (v1)

Minimum viable product — what's needed to validate the concept and go live.

- [ ] Homepage with hero, brief intro, "Join Us" CTA, and section overview — answers "what is this?" and "is it for my kid?" immediately
- [ ] Four section pages (Joeys, Cubs, Scouts, Venturers) with age range, meeting time, and brief activity description — answers "what section suits my child?"
- [ ] Contact / Join Us page with inquiry form (Formspree) — provides the conversion path
- [ ] About page with group background and current leader info — builds trust
- [ ] Term program / events page with placeholder content — demonstrates the group is active; CMS-managed from day one
- [ ] Photo gallery with placeholder/sample images — parents will ask "can I see photos?" before inquiring
- [ ] Responsive design using Scouts QLD branding as base with Cooroy identity layer — professional and trustworthy at all breakpoints
- [ ] CMS integration (Tina or Decap) for at minimum the term program page — volunteers can update content without a developer

### Add After Validation (v1.x)

Features to add once the site is live and the group provides real content.

- [ ] Real group photos replacing placeholders — add when the group leader returns and provides content
- [ ] Fee transparency section on Contact/Section pages — add when exact current fees are confirmed with the group
- [ ] Age progression / section pathway visual on About page — low complexity, add when About page copy is finalised
- [ ] Volunteer recruitment call-out — add after initial launch when the group has reviewed the site

### Future Consideration (v2+)

Features to defer — they add value but aren't needed to go live.

- [ ] CMS expansion to all editable pages — v1 only needs the term program page CMS-managed; expand if volunteers want to update other content independently
- [ ] Social media feed embed (Facebook) — useful if the group actively posts, but an empty/stale feed is an anti-pattern; defer until the feed is consistently active
- [ ] Achievements / badges showcase — could differentiate, but requires ongoing content input from leaders; defer until content supply is proven

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Homepage with hero + CTA | HIGH | LOW | P1 |
| Section pages (x4) | HIGH | LOW–MEDIUM | P1 |
| Contact / Join Us form | HIGH | LOW | P1 |
| Mobile-responsive design | HIGH | LOW | P1 |
| About page | MEDIUM | LOW | P1 |
| Term program / events page | HIGH | MEDIUM | P1 |
| CMS integration | HIGH (for maintainability) | MEDIUM | P1 |
| Scouts QLD branding applied | HIGH (trust) | LOW | P1 |
| Photo gallery | MEDIUM | MEDIUM | P1 |
| "First meeting free" CTA copy | HIGH | LOW | P1 (copy only) |
| Fee transparency | MEDIUM | LOW | P2 |
| Age progression pathway overview | MEDIUM | LOW | P2 |
| Volunteer recruitment call-out | MEDIUM | LOW | P2 |
| Section-specific activity previews | MEDIUM | MEDIUM | P2 |
| Social media feed embed | LOW | MEDIUM | P3 |
| Achievements / badges showcase | LOW | HIGH | P3 |

**Priority key:**
- P1: Must have for launch
- P2: Should have, add when possible
- P3: Nice to have, future consideration

## Competitor Feature Analysis

| Feature | Epping Scouts (NSW) | Ocean Grove Scouts (VIC) | Beenleigh Scouts (QLD) | Our Approach |
|---------|---------------------|--------------------------|------------------------|--------------|
| Section pages | Yes, with activity descriptions | Yes, with badge/pathway detail | Minimal section info | Full section pages with meeting times, activities, and age ranges |
| Meeting times visible | Yes, on section pages | Yes, with cost info | Yes, in parent info | On each section page AND findable from homepage |
| Contact / inquiry | Email link | Contact form | Email to leader | Formspree form — lower friction than raw email |
| Term program / calendar | Yes, news + calendar | Limited | Term dates listed | CMS-managed term program page updated each term |
| Photo gallery | Yes | Yes | No | Yes — static grid, activity-focused, no full-name captions |
| Fee information | Partial (links to fee page) | Yes (mentions cost upfront) | Yes (detailed breakdown) | Publish approximate fees — reduce sticker-shock |
| Mobile responsive | Yes | Yes | Dated/partial | Yes — Tailwind + Astro ensures this by default |
| CMS / volunteer editing | WordPress | WordPress | Plain HTML | Git-based CMS (Tina/Decap) — simpler than WordPress for volunteers |
| Branding | NSW Scouts brand | Scouts VIC brand | Scouts QLD brand (dated) | Scouts QLD brand + Cooroy-specific identity layer |

## Sources

- Ocean Grove Scout Group (VIC): https://www.oceangrovescouts.com — section page structure, uniform/fee info pattern
- Bundoora Scout Group: https://bundoorascouts.org — parent information page depth and topics
- Epping Scout Group (NSW): https://www.eppingscouts.com.au — navigation structure, news + sections pattern
- Beenleigh Scout Group (QLD): http://beenleigh.gcscouts.com.au — Queensland-specific fees, Consent2Go, term dates
- Scouts Queensland membership form: https://scoutsqld.com.au/membershipform/ — official enrollment path
- Scout-Websites.com (UK platform): https://www.scout-websites.com — scout website feature expectations
- NonprofitPRO: https://www.nonprofitpro.com/post/5-website-design-mistakes-nonprofits-should-avoid-in-2025/ — anti-patterns for volunteer-run nonprofit sites
- Scouts UK Digital Safeguarding: https://www.scouts.org.uk/volunteers/staying-safe-and-safeguarding/supporting-young-people/digital-safeguarding/ — child safety guidance for public websites

---
*Feature research for: Scout group informational website (Cooroy Scout Group)*
*Researched: 2026-03-05*
