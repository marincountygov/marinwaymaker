# Marin WayMaker

Marin WayMaker helps County teams create clear decision guides and publish them as interactive web forms.

## What you can do

- Build a step-by-step workflow for staff or residents.
- Add questions, branching paths, and outcome steps.
- Test the full experience before publishing.
- Export a ready-to-use standalone web form.

## Why it helps

- Keeps complex service processes consistent.
- Uses plain-language prompts and labels.
- Supports accessibility-focused form behavior.
- Aligns with Marin County visual and service standards.

## Getting started

1. Open `index.html` in your browser.
2. Enter guide metadata and workflow steps.
3. Test your guide in the runner.
4. Export JSON or a standalone HTML web form.

---

## Technical Overview

This project is currently a single-file app:

- `index.html` - builder UI, runner UI, and export runtime generator

### Core capabilities

- Node types: `question`, `task`, `outcome`, `approval`, `notice`
- Branch operators: `==`, `!=`, `>`, `>=`, `<`, `<=`, `includes`, `>= && <=`, `else`
- Visual flow diagram and live JSON output
- JSON import/export
- Standalone HTML export with embedded flow data

### Standards support

The app has been updated to align with MarinSkills guidance:

- **Brand standards**
  - Official County logo integration:
    `https://www.marincounty.gov/themes/custom/marin_county/logo.png`
  - Constrained logo dimensions in builder, runner, and exported form
  - Inline logo + title/description header layout
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
- In dark mode, logo images apply `filter: invert(100%)`.

### Branch editor behavior note

- If operator is `else`, the Value input is cleared and hidden.
- `else` acts as a fallback when no prior branch condition matches.

### Deployment

- Host the exported HTML as a static file on any server that serves HTML.
