# SPEC.md - Marin Civic Wayfinder

## 1) Purpose

Marin Civic Wayfinder is a browser-based workflow authoring tool that enables County staff to:

1. Design guided decision workflows (node/branch logic)
2. Test workflows interactively
3. Export workflows as:
   - JSON definitions
   - Standalone production-ready HTML forms for end users

The app is implemented as a single-file web application.

---

## 2) Scope

### In scope

- Workflow builder UI
- Interactive test runner (in-app)
- End-user runtime (in-app and exported standalone HTML)
- JSON import/export
- Accessibility-focused form behavior
- MarinSkills-aligned branding and plain-language support
- System preference dark/light mode

### Out of scope

- Server-side persistence/database
- Authentication/authorization
- Multi-user collaboration
- Audit log backend
- Analytics backend
- Automated legal/compliance sign-off

---

## 3) User Roles

### 3.1 Workflow Author (staff)

- Creates and edits workflows
- Defines conditions and branch destinations
- Validates flow integrity
- Exports JSON or HTML runtime

### 3.2 End User (staff/resident)

- Completes an interactive form generated from workflow
- Receives path-dependent guidance/outcomes
- Reviews completion path summary

---

## 4) Functional Requirements

### 4.1 Workflow data model

A workflow contains:

- `meta`
  - `title` (string)
  - `description` (string)
  - `beforeYouStart` (string, optional)
  - `helpContact` (string, optional)
  - `version` (string, optional)
  - `owner` (string, optional)
- `startNodeId` (string)
- `nodes` (object map by node id)

Each node contains:

- `id` (string)
- `type` (enum: `question|task|outcome|approval|notice`)
- `title` (string)
- `prompt` (string)
- `endUserTodos` (array of strings, optional)
- `serviceProviderRequirements` (array of strings, optional)
- `branches` (array)

Each branch contains:

- `id` (string)
- `label` (string)
- `condition` (object)
  - `field` (string; may be empty for `else`)
  - `operator` (enum: `==`, `!=`, `>`, `>=`, `<`, `<=`, `includes`, `>= && <=`, `else`)
  - `value` (any; ignored for `else`)
- `nextNodeId` (string or empty)

### 4.2 Builder requirements

- Author can create, edit, duplicate, delete nodes
- Author can add/remove/edit branches
- Operator select must display human-readable labels while preserving raw stored operator values
- If operator is `else`:
  - Value is cleared
  - Value field is hidden
- Author can set start node
- Live JSON output must update as changes occur
- Flow diagram must reflect nodes/edges and highlight missing targets
- Basic validation must detect structural issues

### 4.3 Preview/Test run requirements

- In-app preview supports step navigation via branch choices
- Captures selected path in preview context
- Preview restart returns to start node

### 4.4 Runner requirements (in-app end-user view)

- Renders metadata (title/description/before-you-start/help contact)
- Supports two interaction modes:
  1. Choice-button mode (branch labels)
  2. Field-entry mode for question nodes with condition fields
- Evaluates branch conditions against entered values
- Supports back/start-over
- Displays path history
- Outputs completion JSON at terminal nodes, including optional final-node lists for:
  - end-user to-dos
  - what to provide to the service provider

### 4.5 Export requirements

- Export JSON: downloads canonical flow JSON
- Export HTML: generates standalone runtime file with embedded flow
- Exported runtime must preserve functional parity with in-app runner for:
  - condition evaluation
  - validation behavior
  - accessibility behavior
  - theming behavior

---

## 5) Accessibility Requirements (WCAG-focused baseline)

### 5.1 Form semantics

- All fields have visible labels associated programmatically
- Related questions grouped with `fieldset` and `legend`
- Required status communicated in UI text and via native required attributes

### 5.2 Validation UX

- On submit errors:
  - Show error summary region
  - Move focus to summary
  - Provide links to invalid controls
- Invalid controls:
  - set `aria-invalid="true"`
  - include error id in `aria-describedby`
- On correction/re-submit:
  - clear prior invalid/error states before re-validating

### 5.3 Keyboard/focus

- All controls keyboard operable
- Visible focus indicators for inputs/buttons/interactive cards
- Focus order is logical and predictable

---

## 6) Visual + Brand Requirements

- Must use official County logo URL:
  - `https://www.marincounty.gov/themes/custom/marin_county/logo.png`
- Logo must be size-constrained and responsive in:
  - builder header
  - runner header
  - exported runtime header
- Header layout:
  - logo inline with header text in desktop layouts
  - stacked gracefully on small screens
- No logo border chrome
- Dark mode logo treatment:
  - `filter: invert(100%)`

---

## 7) Theming Requirements

- Respect system preference using `prefers-color-scheme`
- Provide light and dark design tokens
- Apply to both app UI and exported runtime
- Include `color-scheme: light dark`

---

## 8) Non-Functional Requirements

- Single-file app must run in modern evergreen browsers
- No build step required for basic usage
- Exported runtime must run offline once downloaded (except external logo fetch)
- Must fail gracefully on malformed flow references (missing node targets)

---

## 9) Security and Safety Constraints

- Escape rendered text to avoid HTML/script injection from workflow content
- Escape embedded flow JSON in exported HTML to avoid `</script>` termination issues
- Avoid executing arbitrary expressions from branch definitions

---

## 10) Error Handling Requirements

- Import:
  - reject invalid JSON with clear message
  - reject structurally invalid flow with validation message
- Runtime:
  - if node missing, show user-safe error message
  - if no branch matches, show actionable fallback message to workflow owner

---

## 11) Acceptance Criteria

1. Author can build end-to-end flow and export JSON + standalone HTML.
2. Exported HTML runs without console syntax errors.
3. `else` operator hides/clears Value field in editor.
4. Runner and exported runtime produce equivalent path outcomes for same inputs.
5. Error summary + field-level ARIA states function correctly in both runtimes.
6. Logo is constrained and inline in all headers, with dark mode inversion.
7. App and export follow system dark/light preference automatically.

---

## 12) Future Enhancements (non-blocking)

- Manual theme toggle (optional override)
- Flow schema version migration tooling
- Optional server persistence/API
- Automated accessibility and contrast test suite
- Localization/multilingual content support
