---
name: stack-prs
description: Split one large set of changes into a stack of smaller, separately reviewable PRs.
disable-model-invocation: true
---

# Stack PRs

The output of this skill is a **proposal**: an ordered stack of PRs, presented for the user to approve, reshape, or reject. Cutting branches happens in the last step, and only on their word.

## What makes a stack

A **stack** is an ordered chain of PRs, each based on the one below it. Two properties make it worth the extra branches:

- **Ordered** — a PR may only build on what sits below it in the stack. A change that needs something from a later PR belongs lower, or in the same one.
- **One context per PR** — each PR carries a whole context: a behavior, an area, a layer, plus everything that makes it make sense on its own. A reviewer judges it without reading the rest, and it leaves the branch green.

Common seams, roughly in stack order: shared setup (fixtures, helpers, config) below the code that uses it; refactors below the behavior change they enable; mechanical or generated edits apart from hand-written ones; then one PR per feature, route, or area.

Context is the unit, never the file. Splitting by path produces PRs a reviewer has to reassemble in their head, and reviews worse than the one big PR it came from — so a file that only makes sense next to its neighbor ships with it. Three to five PRs is a healthy stack.

## Workflow

### 1. Gather the changes

Establish what is in play: the base branch, and whether the changes are committed on a branch, staged, or loose in the working tree. Read the actual diff — hunk by hunk, not the file list. **Done when** you can name every changed file and what the change to it does.

### 2. Map the dependencies

For each change, work out what else in the diff it needs: an import, a fixture, a helper, a renamed symbol, a config key. This is the legwork the grouping rests on — a missed dependency produces a stack that does not build. **Done when** every changed file's dependencies on the other changes are named, including "none".

### 3. Group into a stack

Cut the changes along the seams into an ordered stack. Each entry gets:

- **Title** — one line, in this repo's convention.
- **Files** — the paths it carries, and the hunks when a file splits across PRs.
- **Delivers** — one sentence on what a reviewer gets to judge.
- **Branch** — the branch name it will be cut on, in this repo's naming convention.

When the changes genuinely resist splitting — one tangled feature, or small enough already — say so and recommend a single PR instead. **Done when** every changed file lands in exactly one PR, no PR depends on one above it, every PR's context is nameable in one sentence without referring to its file paths, or you have recommended a single PR.

### 4. Propose it

Present the stack as an ordered list, bottom PR first, and say in one line why you cut it there. Invite the user to merge, reorder, or resplit entries. **Done when** the user has approved a stack or asked for a different grouping.

### 5. Cut the stack

Needs the `gh stack` extension, and every change sitting uncommitted in the working tree — when the work is already committed, soft-reset it back onto the base first so the files are free to split.

Build bottom-up, one layer per PR:

1. `gh stack init --base <base> <bottom-branch>` — starts the stack and checks out its bottom branch.
2. Stage that PR's paths with an explicit `git add <paths>`, commit using the PR's title as the message, then run the repo's validation so the layer lands green. Name the paths every time: `-A` and `-u` sweep the other PRs' files into the wrong commit.
3. `gh stack add <next-branch>` to open the layer above, then repeat step 2 on it.

Once every layer is committed, `gh stack submit --auto --open` pushes all branches and opens the chained PRs, taking each title from its commit message. Report the PR URLs bottom-first. **Done when** every PR in the approved stack is open and its URL is reported.
