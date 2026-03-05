---
phase: 02
slug: content-pages
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-05
---

# Phase 02 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | None — visual/manual validation + build smoke tests |
| **Config file** | None |
| **Quick run command** | `npm run build` |
| **Full suite command** | `npm run build && npm run preview` |
| **Estimated runtime** | ~5 seconds (build) |

---

## Sampling Rate

- **After every task commit:** Run `npm run build`
- **After every plan wave:** Run `npm run build && npm run preview` + manual browser check of all new routes
- **Before `/gsd:verify-work`:** Full suite must be green + all routes navigable
- **Max feedback latency:** 5 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 02-01-01 | 01 | 1 | Brand tokens | Build smoke | `npm run build` | ❌ W0 | ⬜ pending |
| 02-01-02 | 01 | 1 | HOME-01 | Manual visual | `npm run dev` + mobile viewport | ❌ W0 | ⬜ pending |
| 02-01-03 | 01 | 1 | HOME-02 | Manual navigation | `npm run preview` + click cards | ❌ W0 | ⬜ pending |
| 02-02-01 | 02 | 1 | ABUT-01 | Build smoke | `npm run build` | ❌ W0 | ⬜ pending |
| 02-02-02 | 02 | 1 | SECT-01–04, SECT-05, SECT-06 | Manual visual | `npm run preview` + navigate sections | ❌ W0 | ⬜ pending |
| 02-03-01 | 03 | 2 | CONT-01, CONT-02, CONT-03 | Manual integration | `npm run dev` + form submission | ❌ W0 | ⬜ pending |
| 02-03-02 | 03 | 2 | EVNT-01 | Content review | `npm run preview` + events page | ❌ W0 | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `src/pages/join.astro` — covers CONT-01, CONT-02, CONT-03
- [ ] `src/pages/about.astro` — covers ABUT-01
- [ ] `src/pages/events.astro` — covers EVNT-01
- [ ] `src/pages/sections/index.astro` — covers SECT-06 (sections overview)
- [ ] `src/pages/sections/joeys.astro` — covers SECT-01, SECT-05
- [ ] `src/pages/sections/cubs.astro` — covers SECT-02, SECT-05
- [ ] `src/pages/sections/scouts.astro` — covers SECT-03, SECT-05
- [ ] `src/pages/sections/venturers.astro` — covers SECT-04, SECT-05
- [ ] `src/components/SectionProgression.astro` — covers SECT-06
- [ ] Brand token update: `src/styles/global.css` @theme — scout-blue, scout-navy, section colors

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Form submission arrives in email | CONT-02 | Requires Web3Forms key + real email | Submit test form, check inbox |
| hCaptcha blocks without completion | CONT-03 | Requires hCaptcha dashboard activation | Attempt form submission without captcha |
| Real meeting times displayed | SECT-01–04 | Content from group leader | Verify times match provided data |
| Real term program entry | EVNT-01 | Content dependency | Verify entry is real, not placeholder |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 5s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
