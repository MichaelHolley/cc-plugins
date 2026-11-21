# DESIGN.md format

Follows the [Google Labs DESIGN.md spec](https://raw.githubusercontent.com/google-labs-code/design.md/main/docs/spec.md): YAML frontmatter with tokens, then up to eight markdown sections.

## Frontmatter

```yaml
---
name: Acme
description: One-line tagline
colors:
  primary: "#b8422e"
  primary-deep: "#8f2f20"
  neutral-bg: "#faf7f2"
  neutral-ink: "#1a1a1a"
typography:
  display:
    fontFamily: "Cormorant Garamond, Georgia, serif"
    fontSize: "4.5rem"
    fontWeight: 300
    lineHeight: 1
    letterSpacing: "0em"
  body:
    fontFamily: "Inter, system-ui, sans-serif"
    fontSize: "1rem"
    fontWeight: 400
    lineHeight: 1.6
rounded:
  sm: "4px"
  md: "8px"
spacing:
  sm: "8px"
  md: "16px"
components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.neutral-bg}"
    rounded: "{rounded.sm}"
    padding: "16px 48px"
  button-primary-hover:
    backgroundColor: "{colors.primary-deep}"
---
<!-- sources: src/styles/tokens.css, tailwind.config.ts, src/components/ui/ -->
```

- Allowed top-level keys: `name`, `description`, `colors`, `typography`, `rounded`, `spacing`, `components`. No `motion`, `shadows`, or `breakpoints`. Those go in prose.
- **The frontmatter is the source of truth.** Prose may name a token and describe its role, but never restates its value differently.
- References use `{group.token}`. Components may reference primitives; primitives never reference each other.
- Colors: any CSS color. Hex is the default; keep the project's canonical format (`oklch()`, `hsl()`) when that is its source of truth. Use one format.
- Keys: descriptive slugs or the project's own names (`oxblood-deep`, `surface-container-low`). Never rename to framework defaults.
- Typography: one entry per role, and only the props that are real (`fontFamily`, `fontSize`, `fontWeight`, `lineHeight`, `letterSpacing`, `fontFeature`, `fontVariation`).
- Component props are limited to `backgroundColor`, `textColor`, `typography`, `rounded`, `padding`, `size`, `height`, `width`. Variants are sibling keys (`button-primary-hover`).
- Only reused tokens. No empty groups, no one-offs, no invented values.
- The `sources` comment right after the frontmatter lists the files the tokens came from. Sync reads it.

## Body

Use these exact headings in this order. Leave out irrelevant sections. Never rename them or add near-synonyms.

```markdown
# Design System: <Project Title>

## Overview

**Creative North Star: "<Named metaphor>"**

<2-3 paragraphs: personality, density, philosophy, confirmed rejections. Start from the North Star.>

**Key Characteristics:**
- <...>

## Colors

<One sentence on the palette's character.>

### Primary
- **<Descriptive Name>** (`{colors.primary}`): <where and why it is used>

### Secondary
### Tertiary
### Neutral
- **<Descriptive Name>** (`{colors.neutral-ink}`): <text / surface / border role>

**The <Name> Rule.** <short doctrine>

## Typography

**Display Font:** <family> (with <fallback>)
**Body Font:** <family> (with <fallback>)
**Label/Mono Font:** <family, if distinct>

**Character:** <1-2 sentences on the pairing.>

### Hierarchy
- **Display** (<weight>, <size>, <line-height>): <purpose>
- **Headline** / **Title** / **Body** (<include measure, e.g. 65-75ch>) / **Label** (<letter-spacing, case>)

## Layout

<Grid or spatial model, containers, density, breakpoints, spacing rhythm. Exact values only when observed.>

## Elevation & Depth

<Shadows, tonal layering, or a hybrid. If no shadows, say so and say how depth is conveyed.>

### Shadow Vocabulary
- **<Role>** (`box-shadow: <exact value>`): <when to use it>

## Shapes

<Corner and radius strategy, borders, clipping, recurring geometry.>

## Components

### Buttons
<character line>
- **Shape:** <description> (<radius>)
- **Primary:** <color assignment, padding>
- **Hover / Focus / Active:** <treatments, including focus ring and motion>
- **Secondary / Ghost:** <...>

### Cards / Containers
### Inputs / Fields
### Navigation
### Chips
### <Signature Component>

## Do's and Don'ts

### Do:
- **Do** <concrete prescription, exact values where established>

### Don't:
- **Don't** <concrete prohibition confirmed by the code or the user>
```

Only document components that exist.

## Writing rules

- **Description first, value in brackets:** "Gently curved edges (8px radius)", not "rounded-lg". No raw class names.
- **Where and why** for each token, not just what it is.
- **Named Rules:** `**The <Name> Rule.** <short doctrine>`, 1-3 per section, e.g. "**The One Voice Rule.** The primary accent covers at most 10% of any screen. Its rarity is the point." Named rules are the guidance agents remember best.
- **Hard language for real invariants**, soft language for provisional guidance.
- **Audit tests only when grounded** in the code or a confirmed decision. One testable sentence beats a paragraph of principle.
- Group colors by role, not by hue or hex order.

## Seed rules

- The first line after the frontmatter is `<!-- SEED: chosen with the user before implementation; run design-md sync once code exists. -->`
- Frontmatter has only `name` and `description`.
- Sections hold the chosen direction. Values that aren't settled are `[to be resolved during implementation]`.
- Leave out `## Components` entirely.
- Do's and Don'ts list only guardrails confirmed in the interview.
