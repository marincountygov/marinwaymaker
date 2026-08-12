# Plan: bring Marin WayMaker fully into the design system

Status: plan only — not implemented.

## Where it already stands

Marin WayMaker already picked up the shared MarinOS brand bundle in an
earlier pass (`shared/app-brand.css`, `shared/app-shell.js`, vendored fonts,
`BRAND_VERSION`). Checked against the current conventions, the **builder
app** (`index.html`, everything up to the point where it generates an
export) is in good shape already:

- Uses the MarinOS banner, `.app-header`/`.app-footer`/`.app-feedback`
  shell, and the linked MarinOS footer (just added sitewide).
- Headings are already sentence case throughout ("Map complex service paths
  into clear next steps," "Build and publish a decision guide," etc.) — no
  AP-style violations found.
- Status/badge UI already uses `.app-status` and `.app-badge`, not a
  one-off custom treatment.
- Sizing and radii already reference `var(--pico-border-radius)` rather
  than hardcoded pixel values, apart from one exception noted below.

So this plan is narrower than "redo WayMaker" — it's really about one
specific gap, plus one small cleanup item.

## The real gap: the exported/published decision guide

WayMaker's actual output — the thing a resident sees — isn't the builder
app. It's a **self-contained HTML file** the builder generates client-side
(the big template literal starting `return \`<!doctype html>...\`` around
line 1040) for the "Build and publish a decision guide" flow. That file is
downloaded/published independently of this repo, so it intentionally does
not `<link>` to `shared/app-brand.css` or anything else external — it's
meant to keep working as a single portable file with no dependency on
GitHub Pages, this repo's folder structure, or network access. **That
constraint is correct and shouldn't change** — the fix here is not "make it
load the shared stylesheet," it's "make its inlined styles actually match
the tokens," which mostly already holds and needs only a few corrections.

Checked its hardcoded values against the real tokens in
`marin-ui/shared/app-brand.css`:

| Exported guide | Real token | Match? |
|---|---|---|
| light body bg `#f6f7f8` | `--app-bg: #ffffff` | **off** — should be `#fff` |
| light text `#1f1f1f` | `--app-text: #1f1f1f` | exact |
| card border `#d8dee4` | `--app-border: #d8dee4` | exact |
| dark body bg `#151b22` | `--app-bg` (dark): `#101418` | **off** — close but not exact |
| dark text `#f4f7fa` | `--app-text` (dark): `#f4f7fa` | exact |
| dark card bg `#1d252e` | `--app-surface` (dark): `#1d252e` | exact |
| dark card border `#3b4652` | `--app-border` (dark): `#3b4652` | exact |
| accent/button blue `#0777cf` | `--marin-blue: #0777cf` | exact |

So most of it was already hand-matched carefully — only the two body
background values are actually wrong. The more visible problems are
**shape/component mismatches**, not color:

- **Feedback button** (`.feedback` in the export) is a solid blue
  rounded-rect (`border-radius:.375rem`), not the real `.app-feedback`
  pill (bordered, surface background, muted text, accent border/text on
  hover). It reads as a generic "primary action" button, not the
  standard Feedback affordance used everywhere else.
- **Footer** is plain, unlinked text (`<footer class="wrap">MarinOS</footer>`)
  — no border-top divider, no muted color, and (per the change just made
  sitewide) not linked to `https://marincountygov.github.io/marinos/`.
- **Buttons/cards** use `.375rem`/`.5rem` radii instead of the real
  `--pico-border-radius` value (`0.25rem`) — close enough to not look
  broken, but not an exact match.

None of this needs an external stylesheet — it's a handful of literal
value corrections inside the existing inline `<style>` block, keeping the
file exactly as portable as it is today.

## Minor cleanup (low priority)

`index.html` line ~147 hardcodes a dark background/text pair
(`#0b1220`/`#dce5ef`) for what looks like a canvas/preview surface,
independent of `prefers-color-scheme`. This may well be intentional (a
canvas area that's meant to stay dark regardless of site theme, like a
code editor), so this plan doesn't assume it's a bug — flagging it for a
decision, not proposing a fix.

## Rollout

Unlike the SOP/expense-objects work, there's no shared-bundle sync step
here — the fix is entirely inside `marinwaymaker/index.html`'s export
template string:

1. Correct the two off-token background colors (`#f6f7f8` → `#fff`,
   `#151b22` → `#101418`).
2. Restyle `.feedback` in the export template to match `.app-feedback`
   (pill shape, bordered, surface background, accent-on-hover) — inlined
   as literal values, same approach already used for every other color.
3. Restyle the export's `<footer>` to match `.footer-inner`/`.site-footer`
   (border-top divider, muted color) and link "MarinOS" to
   `https://marincountygov.github.io/marinos/`.
4. Align button/card radius literals to `0.25rem` to match
   `--pico-border-radius` exactly.
5. Re-generate a sample exported guide and compare side-by-side against a
   real MarinOS page to confirm parity.

## Open questions before I build this

- **Should the exported guide show any MarinOS banner/switcher at all?**
  Recommend no — it already shows a "Marin WayMaker" brand mark at the top
  (icon + name), and the full app-switcher banner would point residents
  away from the single-purpose guide they're using. Confirm that's the
  right call, or say if it should carry at least a minimal "part of
  MarinOS" mark beyond the footer link.
- **The dark canvas colors in the builder** (line ~147) — intentional
  fixed-dark surface, or should it follow `prefers-color-scheme` like
  everything else?
- **Is the builder app itself considered "done"** for this pass, or should
  I do a full line-by-line audit of it too? Given what I found (already
  using shared shell, status/badge components, sentence-case headings,
  token-based radii), I don't see anything else worth fixing there —
  flag if you want a deeper pass anyway.
