# design-md skill

A Claude Code skill that creates or updates a `DESIGN.md` file for any project, following the [google-labs-code/design.md](https://github.com/google-labs-code/design.md) open specification.

## Install

```
claude skills install zircoz/design.md-skill
```

Or via [skills.sh](https://skills.sh/zircoz/design.md-skill).

## Usage

```
/design-md
```

Run from the root of any project. Claude will:

1. Check whether `DESIGN.md` already exists
2. Scan the codebase for design signals (CSS variables, Tailwind config, design tokens, hex colors, font imports, UI framework usage)
3. **Create** a new `DESIGN.md` with YAML design tokens and human-readable guidance — or **update** the existing one, preserving what's already there

## What it produces

A `DESIGN.md` at your project root combining:

- **YAML frontmatter** — machine-readable design tokens for colors, typography, spacing, border-radius, and components; readable by tools like `design.md lint`, `design.md export`, and AI coding agents
- **Markdown sections** — human-readable rationale for Overview, Colors, Typography, Layout, Elevation & Depth, Shapes, Components, and Do's & Don'ts

### Example output

```markdown
---
version: "1.0"
name: "Acme Design System"
description: "A minimal, high-contrast design system for developer tooling."
colors:
  primary: "#2563eb"
  on-primary: "#ffffff"
  secondary: "#7c3aed"
  on-secondary: "#ffffff"
  neutral: "#64748b"
  surface: "#f8fafc"
  on-surface: "#0f172a"
  background: "#ffffff"
  error: "#dc2626"
  on-error: "#ffffff"
typography:
  display:
    fontFamily: "Inter, system-ui, sans-serif"
    fontSize: "48px"
    fontWeight: 800
    lineHeight: 1.1
    letterSpacing: "-0.03em"
  body-lg:
    fontFamily: "Inter, system-ui, sans-serif"
    fontSize: "16px"
    fontWeight: 400
    lineHeight: 1.6
  label-md:
    fontFamily: "Inter, system-ui, sans-serif"
    fontSize: "14px"
    fontWeight: 600
    lineHeight: 1.4
spacing:
  xs: "4px"
  sm: "8px"
  md: "16px"
  lg: "24px"
  xl: "40px"
  2xl: "64px"
rounded:
  xs: "2px"
  sm: "4px"
  md: "8px"
  lg: "16px"
  full: "9999px"
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.on-primary}"
    padding: "{spacing.sm} {spacing.md}"
    borderRadius: "{rounded.md}"
    fontSize: "{typography.label-md.fontSize}"
    fontWeight: "{typography.label-md.fontWeight}"
  chip:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.primary}"
    padding: "{spacing.xs} {spacing.sm}"
    borderRadius: "{rounded.full}"
---

# Acme Design System

## Overview

Acme's design language is built for clarity and speed ...

## Colors
...
```

## Spec compliance

The generated file is valid per the [design.md spec](https://github.com/google-labs-code/design.md/blob/main/docs/spec.md):

- Passes `design.md lint`
- Exports cleanly to Tailwind config via `design.md export --format tailwind`
- All `{token.references}` resolve to defined tokens
- Sections appear in spec order

## Update behavior

Running `/design-md` on a project that already has a `DESIGN.md`:

- Existing tokens are preserved unless a stronger signal overrides them
- New tokens discovered in the codebase are added
- Version is bumped (`1.0` → `1.1`)
- Prose sections are preserved; gaps are filled in

## What signals it reads

| Source | What it extracts |
|--------|-----------------|
| CSS/SCSS custom properties | Color, spacing, radius tokens |
| `tailwind.config.*` | Full token set |
| `tokens.json` / `theme.ts` | Design token values |
| Hex/RGB/HSL in source files | Most-used colors |
| `@font-face` / Google Fonts imports | Font families |
| `package.json` dependencies | UI framework defaults |
| `README.md` / docs | Brand language and principles |
