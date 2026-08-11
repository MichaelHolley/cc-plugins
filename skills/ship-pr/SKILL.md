---
name: ship-pr
description: Use when the user wants a change delivered as a pull request rather than just a local commit — asking to open a PR for a feature, fix, or issue, or to ship something on its own branch.
---

# Ship PR

Take a change from intent to a review-clean pull request. Every step has a gate; do not cross it until the criterion holds.

## Workflow

### 1. Capture intent

Get what the user wants changed: a pasted description, a linked issue, or a PRD. Read the issue/plan if referenced, then explore the codebase for the relevant files and conventions. If scope is ambiguous, ask before guessing past a user-owned decision. **Done when** you can name the files to change and why.

### 2. Branch

A new branch is required — never work on the current one. Ask the user which branch to base it on (offer the current branch and `main` as defaults), then create the branch from it. **Done when** you are on a fresh branch off the confirmed base.

### 3. Plan

Spawn a subagent (the `Plan` agent) to produce an ordered implementation plan: steps, files touched, tests to add. Present it briefly, then proceed. **Done when** you have a plan naming every step and its files.

### 4. Implement

Build the plan, matching the surrounding code's style. Add or update tests covering the new behavior and its edge cases; if the repo has no test setup, propose the minimal one rather than skip silently. **Done when** every plan step is reflected in code and tests.

### 5. Validate

Detect and run the project's validation scripts; fix failures and re-run until green. Never claim green without running them.

- **package.json first**: run the relevant `scripts` when present — `typecheck`, `lint`, `test`, `build`.
- **Fallback**: if no package.json, use the ecosystem's equivalents.

**Done when** every validation script passes.

### 6. Ship

Show the diff summary and proposed message, confirm, then commit, push the branch, and open the PR (`gh pr create`). If the repo has a PR template fill it out and use it as the PR body. **Done when** the PR exists and its URL is reported.

### 7. Review loop

Spawn a subagent to review the pushed changes and return actionable findings. The loop is **green** only when a review pass returns zero actionable findings — one clean pass, not an assumption.

Each round:

1. Fix every finding from the current pass.
2. Re-run **Validate** (step 5), then commit and push so the PR reflects the fixes.
3. Spawn a fresh review subagent over the updated diff.

Repeat until a review pass comes back green. **Done when** the latest review returned no actionable findings and the PR is up to date.
