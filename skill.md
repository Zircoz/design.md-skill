---
name: design-md
description: Create or update a DESIGN.md file for your project following the google-labs-code/design.md spec. Scans existing code for design signals (colors, fonts, spacing) and produces a self-contained plain-text design system document with YAML tokens and human-readable guidance.
---

<command-name>design-md</command-name>

# Design.md Skill

When this skill is invoked, create or update a `DESIGN.md` file in the project root. The file must follow the [design.md spec](https://github.com/google-labs-code/design.md/blob/main/docs/spec.md): YAML frontmatter containing machine-readable design tokens, followed by markdown sections providing human-readable guidance.

## Step 1 — Detect mode

Check whether `DESIGN.md` already exists in the project root.

- **Exists** → read it fully, then enter **update mode**: preserve all existing tokens and prose, add missing sections, reconcile with any new signals found in the codebase.
- **Missing** → enter **create mode**: build from scratch.

## Step 2 — Gather design signals

Search the codebase for design information. Run these searches in parallel:

1. **CSS/SCSS/Less variables** — look for custom property declarations (`--color-*`, `--font-*`, `--spacing-*`, `--radius-*`) and `$variable` patterns. Read up to 5 most relevant files.
2. **Tailwind config** — read `tailwind.config.*` if present; extract `theme.colors`, `theme.fontFamily`, `theme.spacing`, `theme.borderRadius`.
3. **Design tokens file** — check for `tokens.json`, `design-tokens.json`, `tokens.css`, `theme.ts`, `theme.js`, `colors.ts`, or `colors.js`.
4. **Package.json / dependencies** — identify the UI framework (Material UI, Ant Design, Chakra, shadcn, etc.); note the framework's default design language.
5. **Source files** — grep for hex colors (`#[0-9a-fA-F]{3,8}`), `rgb(`, `hsl(`, `oklch(` in `.css`, `.scss`, `.ts`, `.tsx`, `.js`, `.jsx`, `.vue`, `.svelte` files. Tally the top 10 most-frequent values.
6. **Font references** — grep for `font-family`, `@font-face`, `fontFamily`, Google Fonts import URLs.
7. **README / docs** — skim `README.md`, `docs/`, `CONTRIBUTING.md` for brand language, color names, or design principles.
8. **Existing style guide** — look for `STYLEGUIDE.md`, `design-system.*`, `brand.*`.

If no signals are found for a token category, use sensible neutral defaults and note them in the prose.

## Step 3 — Map signals to DESIGN.md tokens

### Colors

Identify the most frequently used or semantically named colors and map them to these standard names. Use CSS-compatible values (hex preferred for simplicity, `oklch()` for wide-gamut when appropriate):

| Token name     | Meaning                                         |
|----------------|-------------------------------------------------|
| `primary`      | Brand / action color                            |
| `on-primary`   | Text/icon color on primary background           |
| `secondary`    | Supporting brand color                          |
| `on-secondary` | Text/icon on secondary background               |
| `tertiary`     | Accent color for highlights                     |
| `on-tertiary`  | Text/icon on tertiary background                |
| `neutral`      | Mid-tone for borders, dividers                  |
| `surface`      | Default background of UI panels/cards           |
| `on-surface`   | Text/icon on surface                            |
| `background`   | Page-level background                           |
| `error`        | Destructive or error state                      |
| `on-error`     | Text/icon on error background                   |

Add additional semantic tokens (e.g., `success`, `warning`, `info`, `outline`) when they are clearly present in the codebase. Use `{colors.token}` references wherever a computed relationship exists (e.g., a lighter tint of `primary`).

### Typography

Map fonts to these scale levels (add or remove as appropriate):

| Token name         | Usage                         |
|--------------------|-------------------------------|
| `display`          | Hero/banner text              |
| `headline-lg`      | H1 page titles                |
| `headline-md`      | H2 section headings           |
| `headline-sm`      | H3 sub-headings               |
| `body-lg`          | Default prose                 |
| `body-md`          | Secondary prose               |
| `body-sm`          | Captions, helper text         |
| `label-lg`         | Button labels, large          |
| `label-md`         | Button labels, default        |
| `label-sm`         | Tags, chips, badges           |
| `code`             | Monospace / code blocks       |

Each typography token is an object with any relevant subset of:
- `fontFamily` — full stack string, e.g., `"Inter, system-ui, sans-serif"`
- `fontSize` — with unit, e.g., `"16px"` or `"1rem"`
- `fontWeight` — numeric, e.g., `400`, `600`, `700`
- `lineHeight` — unitless ratio or value with unit, e.g., `1.5`
- `letterSpacing` — e.g., `"0"`, `"-0.02em"`
- `fontFeature` — OpenType feature string, e.g., `"'ss01' 1, 'cv01' 1"`
- `fontVariation` — variable font axes, e.g., `"'wght' 400"`

### Spacing

Use a named scale (not raw numbers when a scale is derivable). Standard names: `xs`, `sm`, `md`, `lg`, `xl`, `2xl`, `3xl`. Values must use `px`, `em`, or `rem`.

### Rounded (border-radius)

Standard names: `xs`, `sm`, `md`, `lg`, `xl`, `full`. Use `px` or `rem`.

### Components

Define composite tokens for the most prominent UI elements found in the codebase. Common components:

- `button-primary`, `button-secondary`, `button-ghost`
- `input`, `textarea`
- `card`, `panel`
- `chip`, `badge`, `tag`
- `tooltip`
- `nav`, `sidebar`

Each component token can use any property name relevant to styling. Reference other tokens using `{category.token-name}` syntax. Example:
```yaml
button-primary:
  backgroundColor: "{colors.primary}"
  textColor: "{colors.on-primary}"
  padding: "{spacing.sm} {spacing.md}"
  borderRadius: "{rounded.md}"
  fontSize: "{typography.label-md.fontSize}"
  fontWeight: "{typography.label-md.fontWeight}"
```

## Step 4 — Write DESIGN.md

Produce the file with this exact structure:

```
---
[YAML frontmatter]
---

# [Project Name] Design System

## Overview
## Colors
## Typography
## Layout
## Elevation & Depth
## Shapes
## Components
## Do's and Don'ts
```

### YAML frontmatter schema

```yaml
---
version: "1.0"
name: "<Project name>"
description: "<One sentence describing the design system's personality and purpose>"
colors:
  primary: "<value>"
  on-primary: "<value>"
  secondary: "<value>"
  on-secondary: "<value>"
  tertiary: "<value>"
  on-tertiary: "<value>"
  neutral: "<value>"
  surface: "<value>"
  on-surface: "<value>"
  background: "<value>"
  error: "<value>"
  on-error: "<value>"
  # additional tokens as needed
typography:
  display:
    fontFamily: "<stack>"
    fontSize: "<value>"
    fontWeight: <number>
    lineHeight: <number>
    letterSpacing: "<value>"
  # ... additional levels
spacing:
  xs: "<value>"
  sm: "<value>"
  md: "<value>"
  lg: "<value>"
  xl: "<value>"
  2xl: "<value>"
rounded:
  xs: "<value>"
  sm: "<value>"
  md: "<value>"
  lg: "<value>"
  full: "9999px"
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
    padding: "{spacing.sm} {spacing.md}"
    borderRadius: "{rounded.md}"
  # ... additional components
---
```

### Section content guidelines

**## Overview**
2–4 sentences covering: brand personality (e.g., "trustworthy and minimal"), target emotional response, core design principles (e.g., "clarity over decoration"), and how the design language supports the product's purpose.

**## Colors**
- Describe the color palette strategy (monochromatic, complementary, brand-derived, etc.)
- Explain each semantic role (primary, surface, error) in one sentence
- Include a brief accessibility note: whether the palette meets WCAG AA contrast requirements
- List any usage rules (e.g., "never use `tertiary` for large backgrounds")

**## Typography**
- Name each typeface and explain why it was chosen
- Describe the typographic hierarchy and how each scale level is used
- Note line-height and spacing rationale for readability
- If variable fonts are in use, mention the axes used

**## Layout**
- Describe the grid model (e.g., 12-column, 4-column mobile, CSS Grid vs Flexbox)
- Explain the spacing scale origin (4px base, 8px base, etc.)
- Note breakpoints if discoverable from the codebase
- Describe content max-width and margin strategy

**## Elevation & Depth**
- Describe how visual hierarchy is communicated (shadows, blur, z-index layers, or flat/no-elevation)
- List shadow tokens if present, or describe the flat design rationale
- Note any backdrop-filter/blur usage patterns

**## Shapes**
- Describe the corner-radius philosophy (sharp, soft, pill-heavy, etc.)
- Explain the rounded scale and which components use which level
- Note any asymmetric or brand-specific shape treatments

**## Components**
- For each component defined in the YAML: describe its visual character, states (hover, focus, disabled, loading), and usage rules in 2–4 sentences
- Group related components (e.g., form elements together, navigation elements together)
- Call out any compound patterns (e.g., "chips are always used inside a ChipGroup")

**## Do's and Don'ts**
- 5–10 bullet points in two groups: ✅ Do and ❌ Don't
- Focus on common misuse patterns surfaced by the codebase
- Include accessibility, consistency, and brand-alignment rules

## Step 5 — Update mode specifics

When a `DESIGN.md` already exists:

1. Parse the existing YAML frontmatter — preserve every token that does not contradict a clearly more-authoritative signal found in step 2.
2. For each token found in step 2 that differs from the existing value: add a comment explaining the source of the new value (inline YAML comment), then update the value only if the codebase signal is unambiguous (e.g., the same color appears in 5+ files).
3. Preserve all existing prose sections verbatim, then append or edit content where gaps or contradictions are found.
4. Never remove a section or token without a clear reason — if in doubt, add a `# TODO` comment in the YAML or a note in the prose.
5. Increment the `version` field by a patch increment (e.g., `"1.0"` → `"1.1"`).

## Step 6 — Validate before writing

Before writing the file, mentally check:

- [ ] All `{token.reference}` paths resolve to actual token names defined in the frontmatter
- [ ] No duplicate section headings
- [ ] `version`, `name`, `description` fields are present in frontmatter
- [ ] At least `primary`, `surface`, `on-surface`, `background` colors are defined
- [ ] At least `body-md` or `body-lg` typography token is defined
- [ ] `spacing.md` and `rounded.md` are defined
- [ ] Sections appear in spec order: Overview → Colors → Typography → Layout → Elevation & Depth → Shapes → Components → Do's and Don'ts

## Output

Write the file to `DESIGN.md` at the project root. Then report:

1. Whether the file was **created** or **updated**
2. Which design signals were found and used
3. Which tokens were inferred (not directly found in code) and why
4. Any gaps where defaults were used
5. Suggested next steps (e.g., "verify the `primary` color matches brand guidelines", "add shadow tokens for elevation")
