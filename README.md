# Marin WayMaker

Marin WayMaker helps County teams create clear decision guides and publish them as interactive web forms.

## What you can do

- Build a step-by-step workflow using an outline-first authoring interface.
- Add questions with multiple choice, yes/no, number, or text answers.
- Add information steps and result steps with next-step routing.
- Preview the full experience with back navigation and progress tracking.
- Export a standalone production-ready web form.

## Why it helps

- Keeps complex service processes consistent.
- Uses plain-language prompts and author-friendly labels.
- Supports accessibility-focused form behavior.
- Aligns with Marin County visual and service standards.

## Getting started

1. Open `index.html` in your browser.
2. Enter guide metadata and add workflow steps in the Build view.
3. Preview your guide in the Preview view.
4. Save your workflow JSON or export a standalone HTML web form.

---

## Technical Overview

The product logic remains in one HTML file and consumes a vendored MarinOS brand bundle:

- `index.html` - builder UI, runner UI, and export runtime generator
- `shared/` and `vendor/` - shared styles, Pico.css, and the local Jost font
- `BRAND_VERSION` - installed MarinOS bundle version

### Step types

- **Question** — asks the user to pick an answer and route them forward
  - **Multiple choice** — pick one answer from a list
  - **Yes or no** — fixed two-option boolean
  - **Number** — numeric input with optional comparison rules
  - **Text** — free-text input with optional pattern rules
- **Information** — displays guidance and advances on Continue
- **Result** — shows final guidance with to-do items and provider requirements

### Editor layout

- Outline panel lists all steps in reading order with type badges and route summaries.
- Editor panel shows fields specific to the selected step type.
- Step map dialog provides a zoomable full-flow overview.

### Core capabilities

- Internal step types: `question`, `task`, `outcome`, `approval`, `notice`
- Branch operators: `==`, `!=`, `>`, `>=`, `<`, `<=`, `includes`, `>= && <=`, `else`
- Visual flow diagram and live JSON output
- JSON import and workflow save (JSON download)
- Standalone HTML export with embedded flow data
- Exported web form progress indicator (step count) with back navigation
- Validation with deep links to problematic steps

### Standards support

The app aligns with MarinSkills guidance:

- **Brand standards**
  - Text-only MarinOS banner and footer
  - Inline WayMaker icon without external logo dependencies
  - Shared MarinOS App shell and design tokens
  - Text-only Feedback control in the builder and exported form
- **Digital service design**
  - Supports `beforeYouStart` and `helpContact` metadata
- **Plain language**
  - Human-readable branch operator labels in editor
  - Author-friendly labels (e.g., "If the answer is greater than" instead of `>`)
- **Accessibility (forms/web baseline)**
  - Label/input associations
  - Required-field guidance with `aria-describedby`
  - Field-level error messaging
  - `aria-invalid` updates during validation
  - Keyboard-visible focus states
  - Focus trapping in dialogs and inspector overlay
  - `aria-current="step"` for active step indicators
  - Named remove buttons for screen readers

### Theming

- Supports system-preference dark/light mode in builder and exported form via `prefers-color-scheme`.
- Uses `color-scheme: light dark`.
- Shared light/dark tokens are supplied by the MarinOS brand bundle.
- There is no manual theme toggle or stored theme override.

### Deployment

- Host the exported HTML as a static file on any server that serves HTML.

### Testing with WAVE

Prefer testing a locally served HTTP URL such as `http://localhost:8000/` (`python3 -m http.server 8000`) instead of opening the page with `file://`. Firefox extensions, including WAVE, generally cannot evaluate `file://` pages unless "Allow access to file URLs" is enabled for the extension in `about:addons`. A page that stays gray after WAVE is selected usually means the extension could not evaluate the local page, not that the site added an overlay.
