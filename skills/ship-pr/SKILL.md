---
name: ship-pr
description: The full implementation cycle of a new feature, bugfix or change-request. Use when the user wants a change delivered as a new branch including a Pull-Request and Review.
---

# Ship PR

Take a change from intent to a review-clean pull request. Every step has a gate; do not cross it until the criterion holds.

## Workflow

### 1. Capture intent

Get what the user wants changed: a pasted description, a linked issue, or a PRD. Read the issue/plan if referenced, then explore the codebase for the relevant files and conventions. If scope is ambiguous, ask before guessing past a user-owned decision. **Done when** you can name the files to change and why.

### 2. Branch

The change lands on its own feature branch. Check where you are first: a named branch other than the default one (e.g. a worktree prepared) is already that branch - use it. Otherwise create a new branch, confirming the base with the user. **Done when** you are on a named feature branch dedicated to this change.

### 3. Plan

Spawn a subagent (the `Plan` agent) to produce an ordered implementation plan: steps, files touched, tests to add. Present it briefly, then proceed. **Done when** you have a plan naming every step and its files.

### 4. Implement

Build the plan, matching the surrounding code's style. Add or update tests covering the new behavior and its edge cases; if the repo has no test setup, propose the minimal one rather than skip silently. **Done when** every plan step is reflected in code and tests.

### 5. Validate

Detect and run the project's validation scripts; fix failures and re-run until green. Never claim green without running them.

- **package.json first**: run the relevant `scripts` when present - `typecheck`, `lint`, `test`, `build`.
- **Fallback**: if no package.json, use the ecosystem's equivalents.

**Done when** every validation script passes.

### 6. Ship

Commit, push the branch, and open the PR (`gh pr create`). If the repo has a PR template fill it out and use it as the PR body. **Done when** the PR exists and its URL is reported.

### 7. Review loop

Spawn a subagent to review the pushed changes and fix what it finds. It labels each finding Blocker, High, or Minor, fixes every Blocker and High finding itself, and only reports Minor ones without touching them. The loop is **green** only when a review pass finds zero Blocker or High issues - one clean pass, not an assumption.

Run at most 3 review rounds. Each round:

1. Spawn a fresh review-and-fix subagent over the current diff. It returns the findings, what it fixed, and any Blocker or High it could not fix.
2. If it changed anything, re-run **Validate** (step 5), then commit and push so the PR reflects the fixes.

Stop at the first green pass. If round 3 still leaves Blocker or High findings, stop and report them to the user instead of continuing. **Done when** the latest review found no Blocker or High issues, or 3 rounds have run and the remainder is reported, and the PR is up to date.
