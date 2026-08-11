# Marin WayMaker

Marin WayMaker helps County teams create clear decision guides and publish them as interactive web forms.

## What you can do

- Build a step-by-step workflow for staff or residents.
- Add questions, branching paths, and outcome steps.
- Test the full experience before publishing.
- Export a standalone production-ready web form.

## Why it helps

- Keeps complex service processes consistent.
- Uses plain-language prompts and labels.
- Supports accessibility-focused form behavior.
- Aligns with Marin County visual and service standards.

## Getting started

1. Open `index.html` in your browser.
2. Enter guide metadata and workflow steps.
3. Test your guide in the runner.
4. Save your workflow JSON or export a standalone HTML web form.

---

## Technical Overview

The product logic remains in one HTML file and consumes a vendored MarinOS brand bundle:

- `index.html` - builder UI, runner UI, and export runtime generator
- `shared/` and `vendor/` - shared styles, Pico.css, and the local Jost font
- `BRAND_VERSION` - installed MarinOS bundle version

### Core capabilities

- Node types: `question`, `task`, `outcome`, `approval`, `notice`
- Branch operators: `==`, `!=`, `>`, `>=`, `<`, `<=`, `includes`, `>= && <=`, `else`
- Visual flow diagram and live JSON output
- JSON import and workflow save (JSON download)
- Standalone HTML export with embedded flow data
- Exported web form progress indicator (percent + step count) based on remaining steps

### Standards support

The app has been updated to align with MarinSkills guidance:

- **Brand standards**
  - Text-only MarinOS banner and footer
  - Inline WayMaker icon without external logo dependencies
  - Shared MarinOS App shell and design tokens
  - Text-only Feedback control in the builder and exported form
- **Digital service design**
  - Supports `beforeYouStart` and `helpContact` metadata
- **Plain language**
  - Human-readable branch operator labels in editor
- **Accessibility (forms/web baseline)**
  - Label/input associations
  - `fieldset`/`legend` grouping
  - Required-field guidance
  - Error summary with keyboard focus management
  - Field-level error messaging
  - `aria-invalid` and `aria-describedby` updates during validation
  - Keyboard-visible focus states

### Theming

- Supports system-preference dark/light mode in builder and exported form via `prefers-color-scheme`.
- Uses `color-scheme: light dark`.
- Shared light/dark tokens are supplied by the MarinOS brand bundle.
- There is no manual theme toggle or stored theme override.

### Branch editor behavior note

- If operator is `else`, the Value input is cleared and hidden.
- `else` acts as a fallback when no prior branch condition matches.

### Deployment

- Host the exported HTML as a static file on any server that serves HTML.

### Testing with WAVE

Prefer testing a locally served HTTP URL such as `http://localhost:8000/` (`python3 -m http.server 8000`) instead of opening the page with `file://`. Firefox extensions, including WAVE, generally cannot evaluate `file://` pages unless "Allow access to file URLs" is enabled for the extension in `about:addons`. A page that stays gray after WAVE is selected usually means the extension could not evaluate the local page, not that the site added an overlay.
