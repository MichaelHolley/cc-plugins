---
name: ship-it
description: Use when the user wants a unit of work delivered and committed locally — shipping work, building a feature, fixing a bug, or implementing a phase from an existing plan. Not for opening a PR; use ship-pr for that.
---

# Ship It

## Workflow

### 1. Understand

Read the referenced plan, issue, or PRD and explore the codebase for the files, patterns, and conventions in play. Draft a **spec** — definition of done, files to change, approach — then **grill** the user on every open decision, one question at a time with a recommended answer for each (invoke the `grilling` skill if available). Look facts up rather than asking. Done when the spec is confirmed with no user-owned decision left open.

### 2. Plan

If not already planned, list the ordered steps, files touched, and tests to add. Present it briefly, then proceed.

### 3. Implement

Build the plan in the surrounding code's style. Add or update tests for the new behavior and its edge cases; if the repo has no test setup, propose the minimal one rather than skip silently. Done when every step is reflected in code and tests.

### 4. Validate

Run the project's validation until **green**, fixing failures and re-running. Never claim green without running it. Prefer package.json `scripts` (`typecheck`, `lint`, `test`, `build`); fall back to the ecosystem's equivalents.

### 5. Commit

Show the diff summary and proposed message, and **confirm before committing**. Branch first if on `main`. Don't push or open a PR unless asked.

### 6. Hand Off

Close with a short summary of what changed and how the user can verify it themselves — the concrete command to run, the URL or screen to open, and what they should see.
