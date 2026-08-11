---
name: file-pr
description: File a concise pull request. Use when the user asks to file, open, or create a PR.
---

# File PR

Turn already-committed work into a pull request. For taking a change from intent through implementation to a merged PR, that is `ship-pr`.

## Workflow

### 1. Check for an existing PR

Check whether the current branch already has a pull request open. If one does, report its URL and ask whether to update it; create only when the branch has none. **Done when** you have confirmed the branch has no open PR, or the user has chosen to update the existing one.

### 2. Establish base and diff

Determine the base branch — the repo default unless the branch was cut from another, in which case confirm. Read the commits and changed files this branch carries on top of that base. **Done when** you can name every commit and every file the PR carries.

### 3. Write the title

PR titles land in history as commit messages, so match this repository's convention: read enough recent history to see the pattern, then copy the shape you find there. Within that shape, write one short human-readable line that says why the change matters — the effect on the user or the codebase, not a restatement of the diff. **Done when** the title follows the convention observed in history and reads as a reason rather than an inventory.

### 4. Write the body

Open with the problem, in plain language a reviewer can follow without the diff: what was broken, missing, or in the way. Then explain the solution in a line or two, and how to verify it. Keep the whole thing to what a reviewer needs — file-by-file inventory belongs in the diff, not the body. Link the issue it closes when there is one. If the repo has a pull request template, fill every section of it, applying the same order inside whatever sections it provides. **Done when** the body leads with the problem and the solution follows.

### 5. File it

Push the branch, setting its upstream explicitly if it has none. Show the title and body, confirm with the user, then open the pull request. **Done when** the PR exists and its URL is reported.
