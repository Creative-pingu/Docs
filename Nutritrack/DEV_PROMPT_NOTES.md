# Developer Prompt Notes — NutriTrack Phases (Lessons Learned)

Reusable context to include in prompts for future phase work on the
NutriTrack app repo (Creative-pingu/NutriTrack) and docs repo
(Creative-pingu/Docs). These are gotchas discovered during phase work
that were NOT in the original phase briefs and caused real debugging time.

---

## 1. eval() -> static <script> refactors: top-level `const` collisions

When a phase replaces a runtime `eval(transformedCode)` flow with a static
`<script src="compiled.js">` (e.g. Phase 7b), watch for **duplicate
top-level `const` declarations** that share the global script scope.

- `eval()` runs code in the eval call's own function scope, so `const X`
  inside the eval'd code does NOT collide with a `const X` in the host
  inline script.
- A `<script>` tag runs in the **global script scope**, so a `const X` in
  the loaded file DOES collide with a `const X` in the inline shell script
  -> `SyntaxError: Identifier 'X' has already been declared` -> the entire
  app script aborts before `window._MainApp` is set -> "App not found".

**Concrete case:** `index.html` declared `const APP_VERSION` and the
compiled `NutriTrack.js` (which reads `window.APP_VERSION`) also declared
`const APP_VERSION`. Fix: rename the shell's const (e.g. `SHELL_APP_VERSION`)
and keep only the `window.APP_VERSION = ...` assignment shared.

**General rule:** before loading a new compiled artifact as a static
`<script>`, grep both the artifact and the inline shell script for
top-level `const`/`let`/`var` name collisions. Rename the shell's copy.

---

## 2. Validating deploys: web_fetch is NOT a faithful browser

The `web_fetch` tool converts HTML to markdown and runs a headless renderer
that:
- DOES execute inline `<script>` code (so version badges and step
  checklists render).
- Does NOT reliably execute dynamically-inserted external `<script src=...>`
  tags (it fires `onload` for the resource but may not execute the body).
- Does NOT reliably execute `<script defer src=...>` for external CDN URLs
  in environments where the sandbox blocks outbound network.

**Consequence:** an app that works perfectly in a real browser can show a
false "App not found" / "React failed to load" in web_fetch. Do NOT treat
web_fetch output as proof of browser failure.

**Reliable local validation instead:**
- Use **jsdom** (`runScripts: 'dangerously'`, `resources: 'usable'`) with
  local copies of React/ReactDOM UMD builds (the sandbox can't fetch from
  unpkg). jsdom executes external scripts and surfaces real runtime errors
  like the `const` collision above.
- For pure JS validation: **acorn** `parse(code, { sourceType: 'script' })`
  is closer to browser semantics than Babel's own parser for script-mode
  (non-module) files.
- For logic: extract pure functions and run them in Node.

---

## 3. Loading compiled code: prefer the app's existing dynamic-injection loader

The app's proven loader pattern (used since the Phase 6m reliability fixes)
injects scripts via `document.createElement('script')` + `onload` callbacks
(React, ReactDOM) and then the app script. A `<script defer>` refactor is
spec-correct but diverges from what the target device (iPhone 16e) actually
runs. When replacing `eval()`:
- Keep the dynamic-injection pattern for the new compiled `.js` file.
- Do NOT switch to `<script defer>` unless there is a concrete reason; the
  device-tested path is dynamic injection.

---

## 4. Docs repo: main moves concurrently

The docs repo (`Creative-pingu/Docs`) receives commits from other agents /
the user during a session. Before pushing to main:
- `git fetch origin main` and re-check `git merge-base --is-ancestor
  origin/main HEAD`.
- If main moved, rebase onto `origin/main` and re-resolve conflicts (the
  phases/README.md in particular changes frequently as phases are added/
  completed). Do not assume the branch you created at session start is
  still a clean fast-forward.

---

## 5. Versioning reminder (already in the app-repo prompt, restated for emphasis)

`CACHE_VERSION` in `sw.js` and `APP_VERSION` in `index.html` must be bumped
together, and the `#build-info` badge + version-comment in index.html must
match. Phase 7b landed at v61. Check both files before editing to find the
current version.
