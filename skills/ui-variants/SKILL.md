---
name: ui-variants
description: Use when the user is creating or reworking a UI component, form, or layout and wants to compare options before committing — asking for variants, alternatives, or designs side by side to pick from.
---

# UI Variants

Turn one component request into several genuinely different variants, laid out side by side for the user to pick from, then wire the winner in. **Diverge** wide, then **converge** on one.

Default to **5 variants**. Honor any other count the user gives.

## Workflow

### 1. Classify & scope

Read the request and settle three things before building anything:

- **Type** — is this **display-only** (renders data: cards, tables, badges, stats, empty states, nav) or **interactive** (takes input: inputs, forms, filters, pickers, wizards)? This decides the axis of variety in step 2.
- **Count** — 5 unless the user said otherwise.
- **Stack** — find how components are built in this codebase (framework, styling system, existing primitives) so variants match it and can drop in. Reuse existing tokens/components; don't invent a parallel design system.

**Done when** you can name the type, the count, and the file(s) you'll add variants to.

### 2. Diverge

Build the variants. Each must differ from the others on a **real axis**, not by tweaked padding or swapped colors — a stranger should be able to say *why* two variants are different in one sentence. The axis depends on type:

**Display-only → maximize visual variety.** Push the widest spread of *form*: layout structure (grid / list / inline / stacked), visual hierarchy, density (airy vs compact), elevation & borders vs flat, with/without imagery or iconography, decorative vs utilitarian. Five display variants should look like they came from five different design systems.

**Interactive → maximize UX/control variety.** Push the widest spread of *interaction model*, not paint: the control paradigm (dropdown vs segmented vs radio-cards vs search-as-you-type), single-screen vs stepped/wizard, progressive disclosure vs everything-visible, inline validation vs summary, field grouping and order, keyboard/affordance choices. Vary what changes how it *feels to use*, and let the strongest-UX idea be one of the five.

**Done when** every variant exists, is individually rendered/complete, and differs from each sibling on a nameable axis.

### 3. Lay out side by side

Present all variants together in one comparison view, in a layout that fits the type:

- **Display-only** → a gallery grid, so shapes contrast at a glance.
- **Interactive** → columns or a labeled stack, giving each its full width to breathe.

Label each variant and note its distinguishing axis in a line. Prefer a live view in the project's own stack (a scratch route/story/preview page) so variants render in real styling; fall back to a self-contained HTML gallery when the project can't render them live. **Done when** all variants are visible in one view, each labeled with its axis.

### 4. Converge

Give a short recommendation with a reason, then let the user choose. On their pick: wire the chosen variant into the codebase properly and remove the comparison scaffolding and the discarded variants. **Done when** the winner is integrated and the scratch/comparison artifacts are gone.
