# Completion Report — Phase 10: Recipe Management

**Date:** 2026-08-17
**Phase:** 10 — Recipe Management (Editable recipe templates)
**Status:** ✅ Complete — deployed to `main`, Pages build green, both core features and both follow-up refinements confirmed working on-device by the user.
**Tracking refs:** Project doc phase-10 brief (F1, F2). Not a GitHub issue — no `Closes #N` reference applies.
**App repo commits (on `main`):**
- `ba6da13` — `feat(10): editable recipe quantities + reorder ingredients; v68`
- `b194a07` — `feat(10): drop reorder controls; select-all on amount focus; v69`
- `ed84396` — `feat(10): app-wide select-on-focus; add ingredients on recipe log review; v70`
**App PR:** [#13 — feat(10): editable recipe quantities + reorder ingredients; v68](https://github.com/Creative-pingu/NutriTrack/pull/13) (branch `vibe/phase10-recipe-edit-05523a`, landed on `main` via direct push)
**Version bump:** v67 → v68 → v69 → v70 (`CACHE_VERSION` in `sw.js` + `SHELL_APP_VERSION` in `index.html`, bumped together per Phase 6m root-cause rule; version comment kept in sync each time)

---

## 1. What Was Implemented

### Problem
Recipes were effectively static after creation. The recipe editor supported adding and removing ingredients, but there was no way to **update an existing ingredient's quantity** without removing it and re-adding it, and the recipe log-review step (shown after picking servings/grams, before committing a log entry) let you adjust or remove ingredients but **not add new ones**. Selecting a numeric field also dropped the caret at the left edge of the value, making edits awkward on mobile.

### Brief vs. implementation (data-model note)
The phase-10 brief describes `ingredientLines` as an array of strings (e.g. `"200g apple"`). The actual app data model — established in earlier phases and authoritative here — stores recipe ingredients as an array of objects `{ foodId, foodName, amount_g }` (a `Food` snapshot is captured at log time). This report and the implementation follow the **app-repo data model**; the string `ingredientLines` shape only exists on the Notion-sync / paste-import path and was left untouched.

### Fix 1 — Editable recipe ingredient quantities (V4)
`NutriTrack.jsx` gained `updateIngAmountInRecipe(idx, raw)`:
- Updates an existing ingredient's `amount_g` in place (no remove + re-add round-trip).
- Floors at **1 g** so a stray/empty edit can't zero an ingredient out (preserves V8 — a 1-ingredient recipe stays editable and never collapses to zero).
- Rounds to 0.1 g.
The `recipeCreate` ingredient row was reworked so each row renders the food name + live calorie calc, an inline editable amount `<input>`, and the existing remove (×) button.

### Fix 2 — Reorder ingredients (brief user-flow item)
`moveIngInRecipe(idx, dir)` was added to swap ingredients up/down. **Per user feedback it was then removed** (commit `b194a07`): the ▲/▼ controls were too small and not important enough to keep. The `moveIngInRecipe` function and its UI were deleted; the ingredient row settled at name + editable amount + remove. This is recorded here for traceability — reorder is intentionally **not** in the final shipped UI.

### Refinement 1 — Select-on-focus for the recipe amount box
Commit `b194a07` added `onFocus={e=>e.target.select()}` to the recipe-editor amount input so the whole value is selected on focus (edits start at the end of the number, easy to delete/replace).

### Refinement 2 — App-wide select-on-focus
Commit `ed84396` generalised refinement 1 to **every** number/text/search input in the app via a single mount-time `useEffect` that attaches a `focusin` listener to `document`:
- On focus, any `<input>` whose `type` is `number`, `text`, `search`, or `""` (typeless text inputs) calls `select()`.
- `focusin` bubbles, so it covers dynamically rendered inputs and any added in future — no per-input wiring needed.
- Checkboxes, email, and non-`<input>` elements are excluded (the predicate checks `tagName === "INPUT"` and `typeof t.select === "function"`).
- Wrapped in `try/catch` so a non-selectable target can't throw.
The earlier per-input `onFocus` on the recipe amount box was left in place as a harmless redundant fallback.

### Fix 3 — Add ingredients on the recipe log review page
Commit `ed84396` added the ability to add new ingredients on the `recipeLogReview` view (previously edit/remove only):
- New state: `reviewAddOpen` (toggle) + `reviewAddAmount` (grams).
- New action `addIngredientToReview(food)`:
  - Reads `reviewAddAmount`, floors invalid/0/NaN to a 100 g default, rounds to 0.1 g.
  - Pushes `{ foodId, foodName, amount_g, snapshot }` into `recipeLogReviewIngredients`, where `snapshot = buildFoodSnapshot(food)` so the committed log entry is self-contained.
  - Resets the amount/search and closes the panel.
- UI: a "+ Add ingredient" toggle button opens an inline food-search panel (reusing the existing `recipeIngSearch` / `filteredIngFoods` / `groupedIngByCategory` search pipeline and `S.srchItem` styling) with an amount field. Tapping a food adds it to the review list.
- **W1 invariant preserved:** the saved recipe definition is never touched — the new ingredient lives only in the per-log review list and flows into the committed log entry via the existing `commitLogRecipe(finalDI)` path (which already filters `amount_g > 0`).

### Files changed (4 files in the app repo)
| File | Change |
|------|--------|
| `NutriTrack.jsx` | Added `updateIngAmountInRecipe` (+ removed `moveIngInRecipe` after feedback); reworked the `recipeCreate` ingredient row (name + editable amount + remove, with select-on-focus); added the document-level `focusin` select-on-focus `useEffect`; added `reviewAddOpen`/`reviewAddAmount` state, `addIngredientToReview` action, and the "+ Add ingredient" panel on the `recipeLogReview` view. |
| `NutriTrack.js` | Regenerated build artifact (Babel preset-react classic runtime, preset-env `modules:false`). 0 ESM imports/exports, `window._MainApp` footer intact, `node --check` passes. |
| `sw.js` | `CACHE_VERSION` `nutritrack-v67` → `v70` (stepped through v68/v69/v70). |
| `index.html` | `SHELL_APP_VERSION` `'v67'` → `'v70'`; matching version comment updated each bump. |

---

## 2. Validation Outcome

| Test | Where | Result |
|------|-------|--------|
| V1 — Open recipe for editing → Edit interface displayed | On-device | ✅ **Pass (user-confirmed)** — "Both are working great." Recipe detail → Edit opens the editor. |
| V2 — Add ingredient to recipe | On-device | ✅ **Pass (user-confirmed)** — "+ Add" flow on the recipe editor adds ingredients (pre-existing, re-verified). |
| V3 — Remove ingredient from recipe | On-device | ✅ **Pass (user-confirmed)** — × button removes an ingredient from the editor. |
| V4 — Update ingredient quantity | On-device | ✅ **Pass (user-confirmed)** — inline amount input on each ingredient row updates the quantity. |
| V5 — Save edited recipe → changes persisted | On-device | ✅ **Pass (user-confirmed)** — Save writes `recipes` to localStorage; `useEffect` persistence unchanged. |
| V6 — Log edited recipe → logged with updated ingredients | On-device | ✅ **Pass (user-confirmed)** — `logRecipe` derives ingredients from the saved recipe; committed log entry carries the updated amounts. |
| V7 — Cancel edit → original recipe unchanged | Design + on-device | ✅ **Pass** — `recipeInProgress` is a separate copy; Back (cancel) discards it without writing. User confirmed edits behave correctly. |
| V8 — Edit a recipe with 1 ingredient (no minimum enforced) | Logic + on-device | ✅ **Pass** — no minimum to *open* the editor; `updateIngAmountInRecipe` floors at 1 g so a 1-ingredient recipe stays intact. |
| V9 — Device validation on iPhone 16e | On-device | ✅ **Pass (user-confirmed)** — "Both are working great, please update the documentation." |
| Select-on-focus — all inputs | On-device | ✅ **Pass (user-confirmed)** — tapping any input selects its value; user explicitly approved this after the v70 deploy. |
| Add ingredients on recipe log review | On-device | ✅ **Pass (user-confirmed)** — "+ Add ingredient" panel adds ingredients to the review list; user explicitly approved this after the v70 deploy. |
| JSX parse — Babel classic runtime | Local (`/tmp`) | ✅ **Pass** — `@babel/preset-react` `runtime:"classic"` → `PARSE_OK` (1131/1144 `React.createElement` calls across the v68→v70 builds; 0 static imports). |
| Build artifact `node --check` | Local | ✅ **Pass** — `node --check NutriTrack.js` OK on v68, v69, and v70. |
| Logic smoke-test — `updateIngAmountInRecipe` | Local (Node) | ✅ **Pass** — decimals round to 0.1 g; `0`/`NaN`/negative floor to 1 g; only the target index updates. |
| Logic smoke-test — `addIngredientToReview` | Local (Node) | ✅ **Pass** — default 100 g; decimals round; `0`/`NaN` → 100 g; `buildFoodSnapshot` attached; review list grows; saved recipe untouched. |
| Logic smoke-test — focusin predicate | Local (Node) | ✅ **Pass** — selects `number`/`text`/`search`/`""`; excludes `checkbox`/`email`/non-`<input>`. |
| Version sync — `CACHE_VERSION` + `SHELL_APP_VERSION` together | Local | ✅ **Pass** — both at `v70`; `index.html` version comment updated to match (`nutritrack-v70`). |
| Pages build | Deployed (`main`) | ✅ **Pass** — `gh api .../pages/builds/latest` → `status: built`, commit `ed8439693a47e4844c24fad8ce77fd63c30d0463`, no error. |

---

## 3. Delivery Path

- Implementation on branch `vibe/phase10-recipe-edit-05523a` in the app repo, branched off `origin/main` (clean fast-forward ancestry verified before each push).
- Draft PR [#13](https://github.com/Creative-pingu/NutriTrack/pull/13) opened and updated in place across all three commits.
- Because the environment blocks CLI/API PR merges and Pages builds from `main`, each commit was pushed directly to `main` via `git push origin <sha>:main` (clean fast-forwards: `f8fd0f6..ba6da13`, `ba6da13..b194a07`, `b194a07..ed84396`) **with explicit user approval** for the default-branch write.
- GitHub Pages build for the final commit `ed84396` completed successfully (`status: built`).
- The user iterated twice during this phase: (1) requested removal of the reorder buttons + select-on-focus for the recipe amount box → v69; (2) requested app-wide select-on-focus + add-ingredients on the recipe log review page → v70. Both were implemented and confirmed working.

---

## 4. Open Notes / Follow-ups

- **Reorder ingredients:** intentionally removed per user feedback (too-small controls, low value). If recipe ingredient ordering becomes a real need later, it can be reintroduced with larger touch targets as a separate, scoped change.
- **Select-on-focus scope:** the document-level `focusin` listener covers all current number/text/search inputs and future ones. It intentionally does **not** select `checkbox`/`email`/`password` inputs (those don't benefit from full-select and email-select can be surprising on mobile).
- **Recipe log review "add" path:** adds against `allFoods` (excludes soft-deleted custom foods, matching the recipe-creation search). If a user wants to add a brand-new custom food mid-review, they should still create it first via Settings → Custom Foods (same constraint as the recipe editor's "+ Add" → "+ Custom" flow); wiring a custom-food shortcut into the review panel was deemed out of scope.
- This report makes no `Closes #N` reference: F1/F2 are docs-repo feature identifiers, not GitHub issues. Phrase: Resolves project doc F1/F2 (phase-10 brief).

---

## 5. Reference

- Phase brief: [`phase-10/README.md`](./README.md)
- App PR: [#13 — feat(10): editable recipe quantities + reorder ingredients; v68](https://github.com/Creative-pingu/NutriTrack/pull/13)
- Preceding phase: [Phase 9 — Custom Food Promotion and Deployment](../phase-9/README.md)
- Build/versioning root cause: Phase 6m (CACHE_VERSION + APP_VERSION must move together)
