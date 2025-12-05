<!-- Copilot/AI agent instructions for this repository -->
# Copilot instructions — Jaspal portfolio

This is a small static portfolio site. The goal of these instructions is to help an AI coding agent be productive quickly by describing the project's structure, important integration points, and precise patterns to follow when editing code.

1) Big picture
- Single-page static site: entrypoint is `index.html` in the repo root.
- Styling: primarily via Tailwind CDN included in `index.html`. There is an unused `styles.css` file in the repo (not referenced by `index.html`).
- Behavior: dynamic behavior is implemented inline inside `index.html` (near the bottom) and an additional `script.js` file exists but is not imported — check before modifying.

2) Key integration points an agent must know
- Element SDK and Data SDK: `index.html` loads two SDKs from absolute paths: `/_sdk/element_sdk.js` and `/_sdk/data_sdk.js`. These are environment-provided SDKs expected to exist at the host root. If `window.elementSdk` is undefined locally, you will see errors in the browser console. When testing locally either:
  - serve a `_sdk` directory at the site root, or
  - change the script `src` to a local relative path for development.
- Tailwind: loaded from `https://cdn.tailwindcss.com` — no build step is required for CSS utility classes.

3) Runtime configuration pattern (important)
- `index.html` defines a `defaultConfig` object and three functions passed to the SDK during initialization:
  - `onConfigChange(config)` — updates DOM text and colors (example: `document.getElementById('name').textContent = config.name || defaultConfig.name`).
  - `mapToCapabilities(config)` — returns recolorable capabilities used by the host/editor.
  - `mapToEditPanelValues(config)` — maps config keys to editable values.
- If you need to change copy or default colors, edit `defaultConfig` or rely on the SDK `setConfig` path exposed in `mapToCapabilities.set` (calls `window.elementSdk.setConfig({...})`).

4) Project-specific conventions and gotchas
- Inline-first pattern: most interactive logic and configuration live inline in `index.html`. Prefer small, targeted edits there when changing content or the config flows.
- Unreferenced helper files: `script.js` defines `showTime()` but `index.html` does not include it — either import it explicitly or incorporate its logic into `index.html` if you need the clock feature.
- Styles: there is minor inline CSS in the `index.html` <style> block and a separate `styles.css` file that is not linked. Avoid conflicting global CSS rules that override Tailwind utility classes unless intentional.
- Absolute SDK paths: `/_sdk/*` are absolute. For local debugging, replace leading `/` or create a `_sdk` folder at server root.

5) Developer workflows / quick commands
- No build/test scripts are present. The simplest way to run the site locally is to serve the repo root with a static server. Example (PowerShell):
```powershell
# from the repository root
python -m http.server 8000
# or, if Node.js is available
npx serve -l 8000
```
- In VS Code, use the Live Server extension (open `index.html` and click "Go Live").

6) Debugging hints
- If `window.elementSdk` is undefined, open DevTools console — the SDK scripts at `/_sdk/*` failed to load. Either host `_sdk` locally or remove/change the absolute path to test locally.
- DOM IDs used by the config flow: `name`, `title`, `tagline`, `about-text`, `email`, `phone`, `location`, `footer-name`. Use these IDs when updating content programmatically.
- Colors are applied to elements using class selectors like `.gradient-bg` and `.bg-white` in the inline `onConfigChange` code. If color updates don't show, confirm those selectors still exist on modified elements.

7) Making changes safely
- Preserve the `elementSdk.init({...})` call unless you are intentionally removing host integration.
- Keep the `defaultConfig` shape (keys used by `mapToEditPanelValues` and `mapToCapabilities`) when adding/removing fields to avoid breaking the host editor integration.
- If you add JavaScript files, either import them in `index.html` or consolidate into the inline script near the bottom to match the project's current pattern.

8) Examples (concrete edits an agent might perform)
- Change the displayed name: update `defaultConfig.name` in `index.html` or call `window.elementSdk.setConfig({ name: 'New Name' })` if `elementSdk` is available.
- Add a new editable field: add the key to `defaultConfig`, include it in `mapToEditPanelValues`, and update `onConfigChange` to apply it to the DOM.

If anything here is unclear or you want me to expand any section (e.g., include a short dev `README` or convert inline scripts into separate files), tell me which part to iterate on.
