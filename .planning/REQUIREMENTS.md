# Requirements: Cooroy Scout Group Website

**Defined:** 2026-03-05
**Core Value:** Parents can quickly find what Cooroy Scouts offers, which section suits their child's age, and how to join — without needing to track down a leader.

## v1 Requirements

Requirements for initial release. Each maps to roadmap phases.

### Homepage

- [ ] **HOME-01**: User sees hero section with group intro and prominent "Join Us" CTA
- [ ] **HOME-02**: User sees overview of available sections with links to section pages

### Sections

- [ ] **SECT-01**: User can view Joeys page with age range (5-7), meeting time/location, and brief description
- [ ] **SECT-02**: User can view Cubs page with age range (8-10), meeting time/location, and brief description
- [ ] **SECT-03**: User can view Scouts page with age range (11-14), meeting time/location, and brief description
- [ ] **SECT-04**: User can view Venturers page with age range (15-17), meeting time/location, and brief description
- [ ] **SECT-05**: Each section page shows activity previews (what a typical night looks like)
- [ ] **SECT-06**: User can see age progression pathway (Joeys → Cubs → Scouts → Venturers) overview

### About

- [ ] **ABUT-01**: User can view About/History page with group background and scouting info

### Events

- [ ] **EVNT-01**: User can view current term program/activities on events page
- [ ] **EVNT-02**: Volunteer editor can update term program content via browser-based CMS

### Contact

- [ ] **CONT-01**: User can submit inquiry via contact/join us form (name, child's age, email)
- [ ] **CONT-02**: Form submissions forwarded to group email via Web3Forms
- [ ] **CONT-03**: Form includes spam protection (Cloudflare Turnstile or hCaptcha)

### Gallery

- [ ] **GALL-01**: User can view photo gallery of group activities
- [ ] **GALL-02**: Gallery images are optimized (WebP, responsive sizes)

### Design & Infrastructure

- [ ] **INFR-01**: Site is responsive across mobile, tablet, and desktop
- [ ] **INFR-02**: Site uses Scouts Queensland branding with unique Cooroy identity
- [ ] **INFR-03**: Site deployed on Cloudflare Pages with CI/CD from GitHub
- [ ] **INFR-04**: Sveltia CMS integrated with GitHub OAuth for volunteer editing

## v2 Requirements

Deferred to future release. Tracked but not in current roadmap.

### Homepage Enhancements

- **HOME-03**: "First meeting is free" / low-barrier trial CTA on homepage
- **HOME-04**: Volunteer recruitment call-out section

### Section Enhancements

- **SECT-07**: Fee transparency — approximate costs (registration, term fees, uniform) on section or contact pages

### CMS Expansion

- **CMS-01**: All pages editable via CMS (v1 only requires term program)

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Member login / authentication | Not needed for informational site. Use Scouts Member Management portal for private content. |
| Online payments / registration | Requires backend / PCI compliance. Link to official Scouts QLD registration form instead. |
| Real-time calendar with RSVP | Maintenance burden for volunteers. Term program page serves this purpose. |
| News / blog feed | Volunteer groups rarely maintain blogs. Stale posts harm trust. Use Facebook for news. |
| Downloadable forms | Official Scouts QLD forms change — link to official sources to stay current. |
| Chat widget / live support | Nobody will monitor it. Unanswered chat is worse than no chat. |
| Full children's names in photo captions | Digital safeguarding risk per Scouts Australia guidance. Use activity descriptions only. |
| Map embed showing meeting location | Safeguarding concern — reveals where children gather publicly. Send address via email to confirmed enquiries. |
| Social media feed embed | Only useful if group actively posts. Stale feed is an anti-pattern. Defer to v2+. |
| Mobile app | Web-first. Not needed for an informational site. |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| HOME-01 | Phase 2 | Pending |
| HOME-02 | Phase 2 | Pending |
| SECT-01 | Phase 2 | Pending |
| SECT-02 | Phase 2 | Pending |
| SECT-03 | Phase 2 | Pending |
| SECT-04 | Phase 2 | Pending |
| SECT-05 | Phase 2 | Pending |
| SECT-06 | Phase 2 | Pending |
| ABUT-01 | Phase 2 | Pending |
| EVNT-01 | Phase 2 | Pending |
| EVNT-02 | Phase 4 | Pending |
| CONT-01 | Phase 2 | Pending |
| CONT-02 | Phase 2 | Pending |
| CONT-03 | Phase 2 | Pending |
| GALL-01 | Phase 3 | Pending |
| GALL-02 | Phase 3 | Pending |
| INFR-01 | Phase 1 | Pending |
| INFR-02 | Phase 1 | Pending |
| INFR-03 | Phase 1 | Pending |
| INFR-04 | Phase 4 | Pending |

**Coverage:**
- v1 requirements: 20 total
- Mapped to phases: 20
- Unmapped: 0

---
*Requirements defined: 2026-03-05*
*Last updated: 2026-03-05 after roadmap creation*
