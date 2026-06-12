---
name: bemit
description: >
  BEMIT (BEM + ITCSS) methodology skill by Harry Roberts — the union of BEM
  naming and ITCSS layered architecture with namespace prefixes. Use this
  skill whenever the user asks about BEMIT, combining BEM with ITCSS,
  namespaced BEM classes (o-, c-, u-, t-, s-), CSS architecture with
  namespace prefixes, BEM with ITCSS layers, responsive utility suffixes,
  CSS scopes and hacks (._), JS hooks (js-), QA hooks (qa-), or any
  front-end project needing the BEMIT approach to scalable CSS/SCSS. Also
  use when the user wants to audit existing CSS for namespace violations,
  generate a BEMIT project structure, or understand how BEM naming fits
  within ITCSS layers.
license: Complete terms in LICENSE.txt
metadata:
  version: 1.0
  author: Marcos Cazaux
---

# BEMIT — BEM + ITCSS Methodology

This skill extends the **BEM** and **ITCSS** skills. BEMIT is the combined
methodology created by Harry Roberts that applies BEM naming (Block-Element-
Modifier) within the ITCSS layered architecture using namespace prefixes.

## Prerequisites

**This skill requires the `bem` and `itcss` skills to be installed first.**
BEMIT is the union of both methodologies — you need the full BEM naming rules
and ITCSS layer definitions from those skills. This skill only documents what
BEMIT adds beyond each one individually.

---

## What BEMIT Adds

BEMIT contributes three things beyond BEM + ITCSS separately:

1. **Namespace prefixes** — Each ITCSS layer gets a prefix that encodes its
   purpose in the selector name.
2. **Responsive suffixes** — Utility classes can carry `@<breakpoint>` suffixes
   for responsive variants.
3. **Extended layers** — Themes, Scopes, and Hacks sit beyond the standard 7
   ITCSS layers.

---

## Namespace Prefixes

Every class in a BEMIT project carries a prefix that identifies its layer.
This makes the intent of each selector visible in the HTML.

| Prefix         | Layer      | Purpose                                | Example                             |
| -------------- | ---------- | -------------------------------------- | ----------------------------------- |
| `o-`           | Objects    | Non-cosmetic layout patterns           | `.o-grid`, `.o-media`, `.o-wrapper` |
| `c-`           | Components | Specific UI pieces                     | `.c-card`, `.c-btn`, `.c-nav`       |
| `u-`           | Utilities  | Override helpers (`!important`)        | `.u-hidden`, `.u-text-center`       |
| `t-`           | Themes     | Theme overrides (usually on `<body>`)  | `.t-dark`, `.t-high-contrast`       |
| `s-`           | States     | JavaScript-bound state classes         | `.s-active`, `.s-open`              |
| `is-` / `has-` | States     | Temporary state flags                  | `.is-active`, `.has-loaded`         |
| `js-`          | JS Hooks   | JavaScript selection only (no styling) | `.js-modal`, `.js-dropdown`         |
| `qa-`          | QA Hooks   | Testing / automation only (no styling) | `.qa-submit-btn`, `.qa-nav`         |
| `._`           | Hacks      | Short-term overrides (technical debt)  | `._mt-0`, `._fix-ie`                |

### Naming Rules

- Every component class begins with its prefix and the component name in
  kebab-case: `.c-user-card`, `.o-media__figure`.
- Elements and modifiers follow standard BEM after the prefix:
  - `o-media__figure` — element of a prefixed block
  - `c-card--featured` — modifier of a prefixed block
  - `c-card__title--large` — element modifier
- `js-` and `qa-` classes are never styled in CSS — they exist only in HTML
  for binding and testing.
- `._` hacks should be temporary and tracked as technical debt. They override
  specificity with double-underscore convention from the original BEMIT naming.

### Reserved Prefixes

BEMIT also recognises these prefixes from the wider ecosystem:

| Prefix       | Use                                             |
| ------------ | ----------------------------------------------- |
| `_` (single) | Private / internal — not part of the public API |
| `no-`        | Feature-detect negation (Modernizr)             |

---

## Responsive Suffixes

Utility classes can append `@<breakpoint>` to scope them to a viewport:

```scss
// Base: applies everywhere
.u-hidden {
  display: none !important;
}

// Responsive: applies only at md and up
.u-hidden@md {
  @media (min-width: 768px) {
    display: none !important;
  }
}

.u-hidden@lg {
  @media (min-width: 992px) {
    display: none !important;
  }
}
```

```html
<div class="u-hidden@md u-visible@lg">Visible on mobile, hidden on tablet, visible on desktop</div>
```

The breakpoint suffixes should match the project's breakpoint map (`sm`, `md`,
`lg`, `xl`). Only utility classes carry responsive suffixes — components and
objects use their own `@media` queries internally.

---

## Extended Layers

Beyond the 7 standard ITCSS layers, BEMIT defines three additional layers that
sit after Utilities in the triangle:

```
┌─ Settings ─────────────────────────────────┐
├─ Tools ────────────────────────────────────┤
├─ Generic ──────────────────────────────────┤
├─ Elements ─────────────────────────────────┤
├─ Objects (o-) ─────────────────────────────┤
├─ Components (c-) ──────────────────────────┤
├─ Utilities (u-) ───────────────────────────┤
├─ Themes (t-) ──────────────────────────────┤  ◄ Extended
├─ Scopes (s-) ──────────────────────────────┤  ◄ Extended
└─ Hacks (._) ───────────────────────────────┘  ◄ Extended
```

### Themes (`t-`)

Theme classes applied to `<body>` or a wrapper to switch entire visual
appearances. They cascade downward via CSS custom properties.

```scss
// _themes.dark.scss (.t-dark applied to <body>)
.t-dark {
  --color-bg: #1a1a2e;
  --color-text: #eee;
  --color-brand: #e94560;
}

.t-dark .c-card {
  background: #16213e;
  color: #eee;
}
```

### Scopes (`s-`)

Scoped context classes that modify component appearance within a specific
parent scope. Unlike themes, scopes are local.

```scss
// _scopes.sidebar.scss
.s-sidebar {
  .c-card {
    border: none;
    box-shadow: none;
    padding: 0;
  }

  .c-card__title {
    font-size: 0.875rem;
  }
}
```

### Hacks (`._`)

Short-term overrides for browser-specific issues or urgent patches. These are
technical debt — each should link to a ticket for removal.

```scss
// _hacks.fixes.scss
._fix-ie-min-height {
  min-height: 1px; // IE11 min-height fix
}

._mt-0 {
  margin-top: 0;
}
```

---

## Combined File Structure

A full BEMIT project organises files by ITCSS layer with BEMIT prefixes:

```
styles/
├── settings/
│   ├── _settings.global.scss
│   ├── _settings.colors.scss
│   └── _settings.breakpoints.scss
├── tools/
│   ├── _tools.mixins.scss
│   └── _tools.functions.scss
├── generic/
│   ├── _generic.box-sizing.scss
│   ├── _generic.normalize.scss
│   └── _generic.reset.scss
├── elements/
│   ├── _elements.headings.scss
│   ├── _elements.links.scss
│   └── _elements.images.scss
├── objects/
│   ├── _objects.wrapper.scss
│   ├── _objects.grid.scss
│   └── _objects.media.scss
├── components/
│   ├── _components.buttons.scss
│   ├── _components.card.scss
│   ├── _components.navigation.scss
│   └── _components.footer.scss
├── utilities/
│   ├── _utilities.spacing.scss
│   ├── _utilities.text.scss
│   └── _utilities.clearfix.scss
├── themes/
│   ├── _themes.dark.scss
│   └── _themes.high-contrast.scss
├── scopes/
│   ├── _scopes.sidebar.scss
│   └── _scopes.print.scss
├── hacks/
│   └── _hacks.fixes.scss
└── main.scss
```

### main.scss Entry Point

```scss
// SETTINGS
@import 'settings/global';
@import 'settings/colors';
@import 'settings/breakpoints';

// TOOLS
@import 'tools/mixins';
@import 'tools/functions';

// GENERIC
@import 'generic/box-sizing';
@import 'generic/normalize';
@import 'generic/reset';

// ELEMENTS
@import 'elements/headings';
@import 'elements/links';
@import 'elements/images';

// OBJECTS
@import 'objects/wrapper';
@import 'objects/grid';
@import 'objects/media';

// COMPONENTS
@import 'components/buttons';
@import 'components/card';
@import 'components/navigation';
@import 'components/footer';

// UTILITIES
@import 'utilities/spacing';
@import 'utilities/text';
@import 'utilities/clearfix';

// EXTENDED LAYERS
@import 'themes/dark';
@import 'themes/high-contrast';
@import 'scopes/sidebar';
@import 'scopes/print';
@import 'hacks/fixes';
```

---

## Generator Mode

When the user asks you to **write** or **generate** BEMIT code:

1. Confirm the `bem` and `itcss` skills are available — reference their rules
   for BEM naming and ITCSS layer placement.
2. Identify the layer each piece belongs to and apply the correct prefix:
   - Layout patterns → `o-` (Objects)
   - Visual UI → `c-` (Components)
   - Helpers with `!important` → `u-` (Utilities)
3. Follow standard BEM for elements (`__`) and modifiers (`--`) within the
   prefixed namespace.
4. When generating utility classes, consider responsive `@` suffixes.
5. Suggest file names with layer prefix: `_components.card.scss`.
6. Write SCSS with the prefix as part of the block name.

### Output format

```scss
// _components.user-card.scss

.c-user-card {
  display: flex;
  gap: 16px;
  padding: 16px;
  background: #fff;
  border-radius: 8px;

  &__avatar {
    width: 64px;
    height: 64px;
    border-radius: 50%;
  }

  &__name {
    font-size: 1.25rem;
    font-weight: 600;
  }

  &--featured {
    border: 2px solid gold;
  }
}
```

```html
<div class="o-grid">
  <div class="o-grid__item">
    <article class="c-user-card c-user-card--featured">
      <img
        class="c-user-card__avatar"
        src="..."
        alt="" />
      <h2 class="c-user-card__name">Name</h2>
    </article>
  </div>
</div>
```

---

## Auditor Mode

When the user asks you to **review** or **audit** CSS/SCSS for BEMIT compliance:

1. Check that all classes carry the correct namespace prefix for their layer.
2. Flag missing namespace prefixes on classes that belong to a BEMIT layer.
3. Flag wrong prefixes (e.g., `c-` for something that should be `o-`).
4. Flag styled `js-` or `qa-` classes — these should never appear in CSS.
5. Flag `._` hacks without a removal ticket reference.
6. Flag responsive suffixes on non-utility classes.
7. Flag `!important` outside the Utilities layer.
8. Flag theme/scope/hack rules placed above Utilities in the import order.
9. Verify that BEM naming (elements, modifiers) is correct within prefixed
   blocks.
10. Report each issue with file:line and suggested fix.

### Report format

```
## BEMIT Audit Report

### Violations Found

1. **Missing namespace prefix** — `.card` at line 12
   - Should use `.c-card` (Components layer).
   - Fix: Rename to `.c-card`.

2. **Wrong prefix** — `.o-btn` at line 34
   - Buttons are visual UI, not layout patterns.
   - Fix: Use `.c-btn`.

3. **Styled JS hook** — `.js-modal` at line 56
   - JS hooks must not appear in stylesheets.
   - Fix: Use `.c-modal` for styling.

4. **!important in Components layer** — line 78
   - `!important` belongs only in Utilities.
   - Fix: Move to Utilities or refactor specificity.

5. **Hack without ticket** — `._fix-ie` at line 90
   - Hacks must reference a removal ticket.
   - Fix: Add comment with ticket URL.

### Summary
- Total violations: 5
- Critical: 2 (styled JS hook, !important misuse)
- Warnings: 3 (missing prefix, wrong prefix, untracked hack)
```

---

## Common BEMIT Patterns

### Object + Component composition

```html
<div class="o-wrapper">
  <div class="o-layout">
    <div class="o-layout__item u-2/3@md">
      <article class="c-post">
        <h2 class="c-post__title">Title</h2>
        <p class="c-post__excerpt">Excerpt</p>
      </article>
    </div>
    <div class="o-layout__item u-1/3@md">
      <aside class="c-sidebar s-sidebar">
        <div class="c-card">...</div>
      </aside>
    </div>
  </div>
</div>
```

### Theming

```scss
// Applied to <body>
.t-dark {
  --color-bg: #1a1a2e;
  --color-text: #eee;
}

// Components use the custom properties
.c-card {
  background: var(--color-bg);
  color: var(--color-text);
}
```

### JS-bound state

```scss
// JS adds .s-open to .c-nav when toggled
.c-nav {
  display: none;

  &.s-open {
    display: block;
  }
}
```

```html
<nav class="c-nav js-nav">
  <button class="js-nav-toggle">Menu</button>
  <ul class="c-nav__list">
    ...
  </ul>
</nav>
```

### Responsive utilities

```html
<div class="u-flex@md u-column@sm">Flex row on md+, column on small screens</div>
```
