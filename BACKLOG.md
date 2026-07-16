# Motorpool — Backlog

Working list, roughly in priority order. Knock 'em down top to bottom.

## ⭐ NEXT UP — reframe as a shareable, portfolio-worthy tool
Right now Motorpool reads as one person's build hub. Two threads:
- [ ] **Make it genuinely shareable** — let a new visitor start clean (or from sample data) instead of seeing Jazz's Mustang + CB500F. Options to weigh: a "Demo vs. My Data" split, a one-tap **Reset / Start blank**, seed sample content, and clearer "this is a template you fill in" framing. _(builds on the `mp.*` persistence + backup/restore that already exist.)_
- [ ] **Portfolio story** — write the positioning + case study: what it is, the problem it solves, the build story, and how to feature it on the **portfolio site**, **LinkedIn**, and **jazzharrisstudio.com**. Decide the public demo + write-up. _(Jazz asked to be reminded of this.)_

## Content (needs Jazz's real inputs)
- [ ] **Real Car build candidates** — replace the placeholder Wrangler/4Runner/Tacoma cards with the actual cars being considered (specs, prices, links).
- [ ] **Mustang photos** — add real photos of the 2026 Mustang EcoBoost to the Garage tab (photo upload already works per-vehicle).

## Housekeeping
- [ ] **Delete old merged branches** — GitHub-UI action (proxy blocks branch deletion from the agent).

## Done
### Recent — collapsibility, editability, nav & QA
- [x] Every Moto/Garage/Car section collapsible, **summary-first**, state remembered (`mp.sec.*`) with live-value mirrors
- [x] Editable **cash cap** (drives all-in) + editable **funding note** (cash vs. financed)
- [x] Editable **Car "Money" program** figures; **gear** add / remove / edit; **add-a-mod**; **add-a-candidate** (Moto & Car)
- [x] Candidate fields + checklists persist (`mp.fld.*`, `mp.chk.*`); backup **export / import**
- [x] Section-nav **"jump to" dropdown** + **hero chips wrap** (no more off-screen scroll)
- [x] Money-fig **strike-through fix**; **wordmark optically centered** on mobile
- [x] Route **id/label alignment** (Hub→#hub, Garage→#garage)
- [x] **Service-worker font caching** (offline fidelity); **Market Values tables scroll** (were clipped)
- [x] One-time **tap-to-expand nudge** for first-time users
- [x] QA smoke + UAT pass (0 console errors)

### Earlier
- [x] Initialize repo + GitHub Pages · Mobile-first CSS refactor · Garage tab (2026 Mustang daily + mod list)
- [x] Mod money split (Spent / Still-to-buy / All-in) · Bottom nav + installable PWA · A2HS prompt + service worker
- [x] Rebrand to Motorpool with hex logo · Fix Pages deploy hang (`.nojekyll`) · "What's new" (NEW) banner
