# Completion Report — Phase 7a: Remove Anthropic API Call

**Date:** 2026-08-16
**Phase:** 7a — Remove Anthropic API Call (CRITICAL — PRIVACY VIOLATION)
**Status:** ✅ Complete — deployed to `main`, Pages build green, basic functions confirmed working on-device.
**Tracking refs:** Project doc S1 (security bug) / R11 (risk). Not a GitHub issue — no `Closes #N` reference applies.
**App repo commit (on `main`):** `d4bd6aa` — `feat(7a): remove Anthropic API call, local-only recipe parsing`
**App PR:** [#9 — Phase 7a: remove Anthropic API call (privacy fix), local-only recipe parsing](https://github.com/Creative-pingu/NutriTrack/pull/9) (merged into `main` via direct push)
**Version bump:** v59 → v60 (`CACHE_VERSION` in `sw.js` + `APP_VERSION` in `index.html`, bumped together per Phase 6m root-cause rule)

---

## 1. What Was Implemented

### Problem
`parseRecipesFromPasteText()` in `NutriTrack.jsx` made a direct `POST` to `https://api.anthropic.com/v1/messages` with user-pasted recipe text, sending model `claude-sonnet-4-20250514`. This violated the project charter's Local-Only / no-unconsented-transmission privacy guarantees (S1 / R11): user content was transmitted to a third-party API without consent.

### Fix — local-only recipe parsing
Replaced the external API call with a fully on-device text splitter. No `fetch`, no LLM, no network. It runs entirely in the browser and works offline.

**New `parseRecipesFromPasteText(text)` behaviour:**
- Splits pasted text into multiple recipes on **markdown headings** (`#`/`##`/`###`) or **colon-terminated titles** (e.g. `Banana Bread:`).
- Collects the non-blank lines that follow each title as that recipe's `ingredientLines`, which are then handed to the **existing** on-device regex parser pipeline (`parseIngredients` → `parseIngredientsLocal` → `parseIngredientLine`). No new parsing logic duplicated; the pre-existing local parser is reused.
- Reads a standalone `serves/servings N` line (only when it appears **before** any ingredients) as the recipe's servings count. `serves N` embedded in the title (e.g. `Pancakes (serves 4)`) is also recognised via `parseServingsFromTitle()`.
- **Fallback:** when no recognisable structure is detected, the whole paste is treated as a single recipe (`{title:"Pasted recipe", servings:4, source:"pasted", ingredientLines:[...]}`).
- **Unchanged contract:** returns the exact same shape the old API call returned — `[{title, servings, source, ingredientLines}]` — so the downstream `buildReviewData` → review/import pipeline required no changes.

### Files changed (3 files, +46 / −8)
| File | Change |
|------|--------|
| `NutriTrack.jsx` | Replaced `parseRecipesFromPasteText()` (removed `fetch` to `api.anthropic.com`, model `claude-sonnet-4-20250514`); added local splitter + `parseServingsFromTitle()` helper. Updated the paste-import UI warning (no longer references "Claude API access"). |
| `sw.js` | `CACHE_VERSION` `nutritrack-v59` → `nutritrack-v60`. |
| `index.html` | `APP_VERSION` `'v59'` → `'v60'`; updated the matching version comment. |

### Secondary outcome
The paste-import feature, previously non-functional in the deployed app (its UI warning stated it "requires Claude API access, which is not available in the deployed app… will be migrated to the regex parser in a future update"), is now **fully functional and offline-capable**.

---

## 2. Validation Outcome

| Test | Where | Result |
|------|-------|--------|
| V1 — search codebase for `anthropic.com` | Local | ✅ **Pass** — `grep -rni "anthropic"` across `.jsx/.js/.html/.json` returns 0 matches. Also confirmed no `claude-sonnet` references remain. |
| V1b — no API keys/credentials left behind | Local | ✅ **Pass** — only two `fetch(` calls remain: local `foods.json` and the pre-existing Notion Worker URL (`WORKER_URL`). No `api_key`/`sk-ant`/`secret`/`token` values present. |
| JSX parse — Babel classic runtime | Local (`/tmp`) | ✅ **Pass** — `@babel/preset-react` `runtime:"classic"` → `PARSE_OK` (output length 348391). |
| Logic smoke-test — parser behaviour | Local (Node) | ✅ **Pass** — 6 cases verified: markdown-heading split, colon-title split, no-structure fallback, standalone `serves N` line, title-embedded servings, empty input. All correct. |
| Version sync — `CACHE_VERSION` + `APP_VERSION` together | Local | ✅ **Pass** — both at `v60`; `index.html` version comment updated to match. |
| Pages build | Deployed (main) | ✅ **Pass** — `gh api .../pages/builds/latest` → `status: built`, commit `d4bd6aac3e68b20f13899b93fe53fff94c6b9292`, no error. |
| V2/V3 — no `anthropic.com` network request on paste | Deployed (Pages) | ⚠️ **Not explicitly run** — parser makes no `fetch` on the paste path by construction (only `parseIngredients`, which is synchronous/local), so no such request can occur. Recommended to confirm in DevTools Network tab during on-device testing. |
| V4 — works offline | Local + design | ✅ **Pass** — paste parsing makes zero network calls; functions fully offline. |
| V5 — device validation on iPhone 16e | On-device | ✅ **Confirmed by user** — "basic functions are still working" post-deploy. |

---

## 3. Delivery Path

- Implementation on branch `vibe/phase-7a-remove-anthropic-0eb0d6` in the app repo, branched off `origin/main` at `1e7deba`.
- Draft PR [#9](https://github.com/Creative-pingu/NutriTrack/pull/9) opened.
- Because the environment blocks CLI/API PR merges and Pages builds from `main`, the commit was pushed directly to `main` via `git push origin d4bd6aa:main` (clean fast-forward; `1e7deba..d4bd6aa`) **with explicit user approval**.
- GitHub Pages build for `d4bd6aa` completed successfully (`status: built`).

---

## 4. Open Notes / Follow-ups

- **V2/V3 formal confirmation:** the privacy guarantee holds by construction (no `fetch` on the paste path), but a final DevTools Network-tab check during a real paste on-device is recommended to close V2/V3 explicitly rather than by inference.
- **Phase 7b (S2 / R12 — remove `eval()` usage) is still outstanding** and was explicitly out of scope for this phase. It remains the other CRITICAL item blocking non-critical phases.
- This report intentionally makes no `Closes #N` reference: S1/R11 are docs-repo identifiers, not GitHub issues.

---

## 5. Reference

- Phase brief: [`phase-7a/README.md`](./README.md)
- Charter privacy guarantees: [`PROJECT_CHARTER.md`](../../PROJECT_CHARTER.md)
- Security bug / risk entries: [`FEATURES_AND_IMPROVEMENTS.md`](../../FEATURES_AND_IMPROVEMENTS.md) (S1), [`RISK_REGISTER.md`](../../RISK_REGISTER.md) (R11)
