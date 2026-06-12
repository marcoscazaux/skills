---
name: itcss
description: >
  ITCSS (Inverted Triangle CSS) architecture skill — created by Harry Roberts.
  Use this skill whenever the user asks about organizing CSS/SCSS for large
  projects, structuring stylesheets, CSS architecture, scalability in CSS,
  specificity management, the inverted triangle methodology, combining ITCSS
  with BEM (BEMIT), CSS layers organization (Settings, Tools, Generic, Elements,
  Objects, Components, Utilities), or any front-end project needing a scalable
  and maintainable CSS architecture. Also use when the user mentions CSS
  specificity problems, CSS naming conventions, refactoring CSS architecture,
  or wants to set up a SASS project from scratch following industry best
  practices. This includes requests about OOCSS patterns, utility-first CSS,
  CSS resets, CSS custom properties organization, and component-based styling
  architecture.
license: Complete terms in LICENSE.txt
metadata:
  version: 1.0
  author: Marcos Cazaux
---

# ITCSS — Inverted Triangle CSS

This skill provides comprehensive guidance for architecting, writing, and
auditing CSS/SCSS following the **ITCSS (Inverted Triangle CSS)** methodology
created by Harry Roberts.

It serves two modes:

1. **Generator mode** — create a new ITCSS project structure, layers, and
   components from scratch.
2. **Auditor mode** — review existing CSS/SCSS projects and recommend ITCSS
   restructuring.

---

## Core Principles

ITCSS orders all CSS by three key metrics that form an inverted triangle:

```
                    ▲ Generic / Low specificity / Far-reaching
                   / \
                  /   \
                 /     \
                /       \
               /         \
              /           \
             /             \
            /               \
           /                 \
          /                   \
         ▼ Explicit / High specificity / Localised
```

### The Three Metrics

1. **Generic → Explicit** — Start with broad, catch-all rules (reset), end with
   very specific rules (utility classes).
2. **Low specificity → High specificity** — Avoid Specificity Wars by always
   increasing specificity in the same direction.
3. **Far-reaching → Localised** — Start with rules that affect the entire DOM,
   narrow down to rules affecting single nodes.

### Prerequisites

- **No IDs in CSS** — IDs are specificity heavyweights that break the triangle.
- **Componentised UI** — Build to a widgets/components pattern, not pages.
- **Class-based architecture** — Use classes, not bare HTML elements, for
  scalable styling.

---

## The 7 Layers

```
┌─ Settings ─────────────────────────────────┐  Preprocessor variables, config
├─ Tools ────────────────────────────────────┤  Mixins, functions (no CSS output)
├─ Generic ──────────────────────────────────┤  Reset, normalize, box-sizing
├─ Elements ─────────────────────────────────┤  Bare HTML element styles (h1, a, …)
├─ Objects ──────────────────────────────────┤  Non-cosmetic design patterns (.o-*)
├─ Components ───────────────────────────────┤  Specific UI pieces (.c-*)
└─ Utilities ────────────────────────────────┘  Helpers, overrides, !important
```

### Layer 1: Settings

Global preprocessor variables. Only put settings here that are accessed from
anywhere. Keep it slim — most settings live alongside the partial that uses them.

```scss
// _settings.global.scss
$global-font-size: 16px;
$global-line-height: 1.5;
$color-brand: #1a73e8;
$color-text: #333;
$breakpoint-sm: 576px;
$breakpoint-md: 768px;
$breakpoint-lg: 992px;
$breakpoint-xl: 1200px;
```

Production of no CSS — settings only.

### Layer 2: Tools

Globally available mixins and functions. Do not output any CSS in this layer.

```scss
// _tools.mixins.scss
@mixin respond-to($bp) {
  @if $bp == 'sm' {
    @media (min-width: 576px) {
      @content;
    }
  } @else if $bp == 'md' {
    @media (min-width: 768px) {
      @content;
    }
  } @else if $bp == 'lg' {
    @media (min-width: 992px) {
      @content;
    }
  } @else if $bp == 'xl' {
    @media (min-width: 1200px) {
      @content;
    }
  }
}

@mixin font-size($size) {
  font-size: $size;
  line-height: $global-line-height;
}

@mixin visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  border: 0;
}
```

### Layer 3: Generic

The first layer that produces actual CSS. Very high-level, far-reaching styles.
Seldom modified — usually the same across projects.

```scss
// _generic.box-sizing.scss
*,
*::before,
*::after {
  box-sizing: border-box;
}

// _generic.normalize.scss — third-party normalize or minimal reset
html {
  line-height: 1.15;
  -webkit-text-size-adjust: 100%;
}
```

### Layer 4: Elements

Bare, unclassed HTML element selectors only. Define what `h1` looks like without
a class, what `a` looks like without a class. Last layer where element selectors
appear.

```scss
// _elements.headings.scss
h1,
h2,
h3,
h4,
h5,
h6 {
  margin-top: 0;
  line-height: 1.2;
}

h1 {
  font-size: 2rem;
}
h2 {
  font-size: 1.5rem;
}
h3 {
  font-size: 1.25rem;
}

// _elements.links.scss
a {
  color: $color-brand;
  text-decoration: none;

  &:hover {
    text-decoration: underline;
  }
}
```

### Layer 5: Objects

Class-based selectors for non-cosmetic design patterns. Objects are reusable
layout constructs. Use the `o-` namespace (BEMIT convention).

```scss
// _objects.wrapper.scss
.o-wrapper {
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
  padding-left: 16px;
  padding-right: 16px;
}

// _objects.grid.scss
.o-grid {
  display: flex;
  flex-wrap: wrap;
  margin: -8px;
}

.o-grid__item {
  padding: 8px;
}

// _objects.media.scss — the classic Media Object
.o-media {
  display: flex;
  align-items: flex-start;
}

.o-media__figure {
  margin-right: 12px;
  flex-shrink: 0;
}

.o-media__body {
  flex: 1;
}
```

### Layer 6: Components

The bulk of your work. Specific, designed UI pieces. Use the `c-` namespace.

```scss
// _components.buttons.scss
.c-btn {
  display: inline-block;
  padding: 8px 16px;
  border: 1px solid transparent;
  border-radius: 4px;
  font-weight: 600;
  cursor: pointer;

  &:hover {
    opacity: 0.9;
  }
}

.c-btn--primary {
  background: $color-brand;
  color: #fff;
  border-color: $color-brand;
}

.c-btn--secondary {
  background: transparent;
  color: $color-brand;
  border-color: $color-brand;
}

// _components.card.scss
.c-card {
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.08);
  overflow: hidden;

  &__image {
    width: 100%;
    height: auto;
    display: block;
  }

  &__body {
    padding: 16px;
  }

  &__title {
    font-size: 1.25rem;
    font-weight: 700;
    margin: 0 0 8px;
  }

  &--featured {
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.16);
    border-left: 4px solid $color-brand;
  }
}
```

### Layer 7: Utilities

The tip of the triangle. Utilities override anything before them. They carry
`!important` and use the `u-` namespace.

```scss
// _utilities.spacing.scss
.u-mt-0 {
  margin-top: 0 !important;
}
.u-mt-1 {
  margin-top: 4px !important;
}
.u-mt-2 {
  margin-top: 8px !important;
}
.u-mb-0 {
  margin-bottom: 0 !important;
}
.u-mb-2 {
  margin-bottom: 8px !important;
}
.u-mb-4 {
  margin-bottom: 16px !important;
}

// _utilities.text.scss
.u-text-center {
  text-align: center !important;
}
.u-text-left {
  text-align: left !important;
}
.u-text-right {
  text-align: right !important;
}
.u-hidden {
  display: none !important;
}
.u-visually-hidden {
  @include visually-hidden;
}

// _utilities.clearfix.scss
.u-clearfix::after {
  content: '';
  display: table;
  clear: both;
}
```

---

## BEMIT Naming Convention

ITCSS pairs naturally with **BEMIT** — BEM extended with namespaced prefixes.

| Prefix         | Layer               | Example                       |
| -------------- | ------------------- | ----------------------------- |
| `o-`           | Objects             | `.o-media`, `.o-grid`         |
| `c-`           | Components          | `.c-card`, `.c-btn`           |
| `u-`           | Utilities           | `.u-hidden`, `.u-text-center` |
| `s-`           | States (JavaScript) | `.s-active`, `.s-open`        |
| `is-` / `has-` | Temporary states    | `.is-active`, `.has-loaded`   |
| `t-`           | Theme overrides     | `.t-dark`, `.t-high-contrast` |

```html
<div class="o-wrapper">
  <article class="c-card c-card--featured">
    <img
      class="c-card__image"
      src="..."
      alt="" />
    <div class="c-card__body">
      <h2 class="c-card__title">Title</h2>
      <a
        class="c-btn c-btn--primary"
        href="#"
        >Read more</a
      >
    </div>
  </article>
</div>
```

---

## File Architecture

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
│   ├── _elements.lists.scss
│   ├── _elements.images.scss
│   └── _elements.forms.scss
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
@import 'elements/lists';
@import 'elements/images';
@import 'elements/forms';

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
```

---

## SASS Best Practices with ITCSS

### 1. Layer Order is Sacred

Layers must always appear in this order. Within a layer, partial order does not
matter (they share the same specificity). Only the layer sequence is enforced.

### 2. Limit Nesting (max 2–3 levels)

Deep nesting creates overqualified selectors that fight the triangle.

```scss
// ✅ Good — flat or max 2 levels
.c-card {
}
.c-card__title {
}
.c-card--featured .c-card__title {
}

// ❌ Bad — overqualified and unnecessary
.c-card {
  .c-card__body {
    .c-card__title {
      // Too deep
    }
  }
}
```

### 3. One Component Per File

Each component gets its own partial. Do not mix multiple components in one file.

### 4. Keep Settings and Tools Slim

Only globally-shared items go in Settings/Tools. Component-specific variables
and mixins stay in the component file.

### 5. Utilities are the Escape Hatch

Utilities with `!important` are acceptable because they sit at the tip of the
triangle. They should be the last resort, not a daily driver.

### 6. Objects vs Components

Objects are layout patterns (non-cosmetic). Components are visual UI pieces.
If a pattern has a visual design (colors, borders, fonts), it lives in
Components, not Objects.

### 7. Margin Belongs to the Parent Context

Avoid margins on components. Use wrapper objects or utility spacing classes
to control spacing between components. This keeps components truly reusable.

---

## Generator Mode

When the user asks you to **create** or **set up** ITCSS:

1. Ask about the project type and scope.
2. Generate the full directory structure with all 7 layers.
3. Populate each layer with appropriate partials.
4. Include a `main.scss` entry point with correct import order.
5. For components, use BEMIT naming with proper prefixes.
6. Provide at least one complete example component per layer.

### Output format

```
Project structure overview, then the main.scss, then each layer's files.
```

---

## Auditor Mode

When the user asks you to **review** or **audit** CSS/SCSS for ITCSS compliance:

1. Check that layers appear in the correct order (Settings → Utilities).
2. Flag any CSS output in Settings or Tools layers.
3. Flag any ID selectors — they violate the specificity triangle.
4. Flag deep nesting (> 3 levels).
5. Flag component styles defined in element selectors.
6. Flag missing BEMIT namespaces.
7. Flag `!important` outside the Utilities layer.
8. Flag multiple components defined in a single file.
9. Flag margin on components that should use wrapper/spacing utilities.
10. Report the specificity graph health.

### Report format

```
## ITCSS Audit Report

### Violations Found

1. **CSS output in Tools layer** — `_tools.mixins.scss` line 15
   - The `.clearfix` class generates CSS. Move to Utilities layer.
   - Fix: Move to `_utilities.clearfix.scss`.

2. **ID selector used** — `#header` at line 42 in `_components.navigation.scss`
   - IDs break the specificity triangle.
   - Fix: Replace with `.c-nav` class.

3. **Deep nesting** — 4 levels at line 78 in `_components.card.scss`
   - `.c-card { .c-card__body { .c-card__title { span { } } } }`
   - Fix: Flatten or use a direct class.

4. **!important in Components layer** — line 90
   - `!important` belongs only in Utilities.
   - Fix: Move override to Utilities or refactor specificity.

### Summary
- Total violations: 4
- Critical: 2 (ID selector, !important misuse)
- Warnings: 2 (CSS in Tools, deep nesting)
```

---

## Common ITCSS Patterns

### Responsive component with object composition

```html
<div class="o-grid">
  <div class="o-grid__item o-media">
    <img
      class="o-media__figure c-avatar"
      src="..."
      alt="" />
    <div class="o-media__body">
      <h3 class="c-user-card__name">Name</h3>
      <p class="c-user-card__bio">Bio</p>
    </div>
  </div>
</div>
```

### Theme via t- prefix

```scss
// Applied to <body> or wrapper
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

### State classes with s- prefix (JS-bound)

```scss
.c-nav {
  display: none;

  &.s-open {
    display: block;
  }
}
```
