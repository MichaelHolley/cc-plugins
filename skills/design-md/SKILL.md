---
name: design-md
description: Create or sync a project's DESIGN.md, the visual design system record agents follow to stay on-brand. Use when the user wants a DESIGN.md written, refreshed, or checked against the code, or asks to document the design system, design tokens, or visual style, or says the design docs have drifted.
argument-hint: "[create|sync|seed]"
---

# DESIGN.md

DESIGN.md sits at the project root and records the **durable visual system**: tokens in YAML frontmatter (the source of truth) and prose on how to apply them. The format is in [FORMAT.md](FORMAT.md). Read it before writing.

DESIGN.md covers visuals only. Product purpose and audience go in PRODUCT.md, README, or AGENTS.md, and page-specific strategy stays with the page. Do not copy either in.

**The code is ground truth.** Record what is built and reused, never what was planned. Where intent and code disagree, the code wins, unless the user calls it a defect.

## Pick the mode

- No DESIGN.md and the code has a visual system: **create**.
- DESIGN.md exists: **sync**. When it exists and the user asked for create, show it and ask: refresh (sync), overwrite (create from scratch), or merge (create, keeping confirmed decisions). Never overwrite silently.
- No DESIGN.md and nothing to extract (no tokens, styles, or components): offer **seed**. Don't switch silently.

An explicit argument wins.

## Create

### 1. Scan

Find facts yourself. Never ask the user something the code answers. Search in priority order and note name, value, and source file:

1. CSS custom properties (`--color-`, `--font-`, `--space-`, `--radius-`, `--shadow-`, `--ease-`, `--duration-`).
2. Tailwind config `theme`/`theme.extend`, or Tailwind v4 `@theme` blocks.
3. Theme files for CSS-in-JS (`theme.ts`, `tokens.ts`) and token JSON (Style Dictionary, W3C).
4. Core components: button, input, card, nav, chip, dialog. Note their variants, states, and padding.
5. Global stylesheet: base type, background, and text color.
6. Rendered output, if a browser tool is already available. Use the computed styles of body, h1, links, buttons, and cards.

Count usages. A value earns a token only if it is reused. Record one-offs, near-duplicates (two greys 2% apart), and anything that looks like a defect as **open decisions** for the interview, not as tokens.

Map what you find:

- Colors go into Primary / Secondary / Tertiary / Neutral roles. If there is a single accent, use Primary + Neutral only.
- Type sizes go into display / headline / title / body / label. Note the scale ratio.
- Shadows go into the elevation vocabulary, or record "flat, tonal layering".
- Grid, containers, breakpoints, and spacing rhythm go to Layout. Radius, borders, and clipping go to Shapes.

### 2. Interview

Ask through AskUserQuestion, never as prose questions in your reply. Use **at most two rounds of up to three questions each**, and wait for each round's answers before the next. Combine where you can. Skip questions the code or earlier answers already settle.

**Round 1 - identity:**

- **North Star** - one named metaphor for the whole system ("The Editorial Sanctuary", "The Lab Notebook", "The Quiet Instrument"). Offer 2-3 named options derived from what the scan saw and the product's purpose. Each description says what the name commits the system to.
- **Mood** - 2-3 adjective sets plus a one-line philosophy, and the anti-reference each one rules out.
- **Elevation** - flat / layered / lifted, or ambient vs structural shadows when shadows exist.

**Round 2 - specifics:**

- **Color names** - descriptive names based on hue, saturation, and role ("Deep Muted Teal-Navy", never "blue-800"). Offer 2-3 naming sets for the whole palette, each set's `preview` listing every key color with its name and value.
- **Component feel** - one phrase for buttons, cards, and inputs ("tactile and confident", "refined and restrained").
- **Open decisions** from the scan: which near-duplicate is canonical, whether a one-off is a rule or a defect, which variant counts as the default. Use `preview` to show the competing values or snippets side by side.

Question shape:

- `header` - 1-2 words.
- `question` - the decision plus the evidence behind it ("`#1a1a1a` is used 41x, `#1c1c1c` 3x"), so the user can pick fast.
- `options` - 2-4 real choices. **Always put your recommendation first, with " (Recommended)" on its label**, grounded in the scan. Each `description` says what the DESIGN.md will say or enforce if that option is picked. Never add an "Other" option; the tool adds one.

Unattended (no question tool): take the recommended option for each decision and list every assumption in the final report.

### 3. Write

Write DESIGN.md per [FORMAT.md](FORMAT.md). Fill the frontmatter from the scan, the prose from the scan plus the interview, and the Named Rules and Do's and Don'ts only from evidence or confirmed answers.

Before saving, check every rule against the code:

- A prohibition must not ban something the system itself uses.
- A recorded value must not legitimize a defect. A defect the user confirmed stays out of DESIGN.md and goes in your report.
- A rule specific to one page must not become a system-wide rule.

### 4. Confirm

Show the file. Point out the non-obvious choices (color names, North Star, Named Rules) and any assumptions you made. Offer to revise a section.

## Sync

Keeps DESIGN.md true to the code without losing its voice.

1. **Read** DESIGN.md, and its `sources` comment if present.
2. **Diff** the code against it, scanning as in Create step 1:
   - **Changed** - the token exists in both, but the value differs.
   - **Missing** - a reused value or component in the code that DESIGN.md lacks.
   - **Dead** - a token, component, or source path in DESIGN.md that the code no longer has.
   - **Contradicted** - a prose rule or Do/Don't that the code now violates in several places.
3. **Report** the drift as a compact table (kind, token or rule, DESIGN.md says, code says, file). If there is none, say "In sync" with the sources checked, and stop. Don't write just to prove the check ran.
4. **Decide** with AskUserQuestion, same question shape as the Create interview:
   - Changed / Missing / Dead: update DESIGN.md (Recommended when the change is reused and deliberate) vs. treat the code as a defect to fix. Batch the clear-cut ones into a single "apply all N value updates" question.
   - Contradicted: update the rule to match the code, or keep the rule and list the offending code as drift to fix. Recommend based on how widespread the code pattern is and whether it looks deliberate.
   - A new key color or component: ask for its descriptive name (2-3 options, recommended first).
5. **Apply** with minimal edits: change values in the frontmatter, add or remove entries, and change only the prose sentences that named the changed item. Keep section order, headings, the North Star, and untouched prose as they are. Update the `sources` comment.
6. **Report** what changed, and the code drift the user chose to keep as defects. Fix code only when asked.

When drift is so wide that DESIGN.md no longer describes the system (most tokens changed, a redesign happened), recommend a full **create** with merge instead.

## Seed

For projects with no visual system yet. It records a chosen direction, never made-up tokens.

1. Run the Create interview, with seed-specific questions in round 2: palette strategy (2-3 directions, recommended first), type character (serif/grotesk/mono pairing ideas), and form language (sharp / soft / pill).
2. Write DESIGN.md per [FORMAT.md](FORMAT.md)'s seed rules.
3. Tell the user to run sync (or create) once code exists. That run replaces the seed with real tokens.
