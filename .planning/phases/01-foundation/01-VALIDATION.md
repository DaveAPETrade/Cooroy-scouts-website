---
phase: 1
slug: foundation
status: draft
nyquist_compliant: false
wave_0_complete: false
created: 2026-03-05
---

# Phase 1 — Validation Strategy

> Per-phase validation contract for feedback sampling during execution.

---

## Test Infrastructure

| Property | Value |
|----------|-------|
| **Framework** | None — greenfield project, Phase 1 validation is visual/manual |
| **Config file** | None — Wave 0 creates project scaffold |
| **Quick run command** | `npm run build` |
| **Full suite command** | `npm run build` + manual browser check |
| **Estimated runtime** | ~10 seconds (build) |

---

## Sampling Rate

- **After every task commit:** Run `npm run build` — confirms no TypeScript/Astro compilation errors
- **After every plan wave:** Run `npm run build` + manual browser check of responsive breakpoints
- **Before `/gsd:verify-work`:** All three success criteria verified manually
- **Max feedback latency:** 10 seconds

---

## Per-Task Verification Map

| Task ID | Plan | Wave | Requirement | Test Type | Automated Command | File Exists | Status |
|---------|------|------|-------------|-----------|-------------------|-------------|--------|
| 01-01-01 | 01 | 1 | INFR-01 | Build smoke | `npm run build` | ❌ W0 | ⬜ pending |
| 01-01-02 | 01 | 1 | INFR-01 | Manual visual | `npm run dev` + DevTools responsive | ❌ W0 | ⬜ pending |
| 01-02-01 | 02 | 1 | INFR-02 | Manual visual | Inspect element — check computed styles | ❌ W0 | ⬜ pending |
| 01-02-02 | 02 | 1 | INFR-02 | Manual review | Confirm hex values match brand kit PDF | Manual only | ⬜ pending |
| 01-03-01 | 03 | 1 | INFR-03 | Integration (live) | Push commit + observe CF Pages dashboard | Manual only | ⬜ pending |
| 01-03-02 | 03 | 1 | INFR-03 | Smoke test (live) | `curl -s -o /dev/null -w "%{http_code}" https://{project}.pages.dev` | Manual only | ⬜ pending |

*Status: ⬜ pending · ✅ green · ❌ red · ⚠️ flaky*

---

## Wave 0 Requirements

- [ ] `npm create astro@latest` — scaffold project
- [ ] Install `tailwindcss @tailwindcss/vite` — CSS framework
- [ ] Create `astro.config.mjs` with Vite plugin
- [ ] Create `src/styles/global.css` with `@theme {}` brand tokens

*No automated test framework needed — Phase 1 validation is entirely visual/manual.*

---

## Manual-Only Verifications

| Behavior | Requirement | Why Manual | Test Instructions |
|----------|-------------|------------|-------------------|
| Brand colours match official Scouts QLD PDF | INFR-02 | Requires human visual comparison against official PDF | Open official brand PDF and compare hex values against `global.css` tokens |
| Push triggers Cloudflare Pages rebuild | INFR-03 | Requires live Cloudflare connection | Push to `main`, verify build appears in CF Pages dashboard |
| Site loads over HTTPS | INFR-03 | Requires live deployment | Navigate to `*.pages.dev` URL, verify HTTPS lock icon |
| Responsive layout renders correctly | INFR-01 | Visual rendering judgement | Open in browser DevTools, test at 375px, 768px, 1280px breakpoints |

---

## Validation Sign-Off

- [ ] All tasks have `<automated>` verify or Wave 0 dependencies
- [ ] Sampling continuity: no 3 consecutive tasks without automated verify
- [ ] Wave 0 covers all MISSING references
- [ ] No watch-mode flags
- [ ] Feedback latency < 10s
- [ ] `nyquist_compliant: true` set in frontmatter

**Approval:** pending
