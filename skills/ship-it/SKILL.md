---
name: ship-it
description: "Execute a unit of work end-to-end: plan, implement with tests, validate, then commit. Use when user wants to ship work, build a feature, fix a bug, or implement a phase from a plan."
---

# Ship It

Take a unit of work from intent to committed change: plan, build, validate, commit.

## Workflow

### 1. Understand

Read any referenced plan, issue, or PRD. Explore the codebase for the relevant files, patterns, and conventions. If the task is ambiguous, ask the user to clarify scope before proceeding — don't guess past a user-owned decision.

### 2. Plan

If the task isn't already planned, create a short plan: ordered steps, files touched, tests to add. Present it briefly, then proceed.

### 3. Implement

Build the plan, matching the surrounding code's style.

**Add or update tests** covering the new behavior and its edge cases. If the repo has no test setup, propose the minimal one rather than skipping tests silently.

### 4. Validate

Detect and run the project's validation scripts; fix failures and re-run until green. Never claim green without running them.

- **package.json first**: run the relevant `scripts` when present — `typecheck`, `lint`, `test`, `build`.
- **Fallback**: if no package.json, use the ecosystem's equivalents

### 5. Commit

Once validation passes, show the diff summary and proposed message, and **confirm before committing**. Branch first if on `main`. Don't push or open a PR unless asked.
