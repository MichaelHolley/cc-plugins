---
name: review-loop
description: Review the current change with fresh-context subagents in a capped fix loop until it converges.
disable-model-invocation: true
---

# Review loop

Drive the current change (branch diff against its base, or the working tree) to review-clean. The loop is capped and converging: every round must shrink the open findings, never grow them.

## Workflow

### 1. Intent and risk

State the change's intent in two lines (read the PR, commit messages, linked issue). Read `REVIEW.md` and `CLAUDE.md` if present. Pick a tier from the change itself:

- **light**: docs, copy, additive code behind a flag. 1 round.
- **standard**: ordinary product change. Up to 3 rounds.
- **deep**: auth, payments, persistence or migrations, push, CI or release config, anything many files copy. Up to 3 rounds, plus a security pass.

Done when intent, tier, and round cap are stated.

### 2. Green first

Run the repo's lint, typecheck, and focused tests. Fix red before any review. Reviewers read evidence, not claims.

Done when every check is green and its output is in hand.

### 3. Review

Dispatch a fresh-context subagent (no authoring history) with: the diff, the intent, `REVIEW.md`. It reads the diff against the surrounding code, hunting correctness, safety, and missing tests. Each finding needs a file and line, a severity (`important`, `nit`, `pre-existing`), and a concrete failure scenario. For bug fixes, it checks root cause, not symptom. In deep tier, a second subagent runs the same diff for security only.

Done when every finding carries file, line, severity, and scenario.

### 4. Verify findings

Dispatch one verifier subagent (model `sonnet` if available) per `important` finding. It tries to refute the finding by reading the code and running a check. Keep a finding only if the verifier confirms it. Drop nits, style, and `pre-existing` findings from the fix list (report `pre-existing` separately).

Done when each kept finding is confirmed and every dropped one has a reason.

### 5. Fix

Fix each confirmed finding, and the whole class of the bug: grep for sibling instances. Add a test that fails without the fix. Re-run step 2 checks.

Done when each finding is fixed, or answered with a reason it stays.

### 6. Converge

Re-review **only the new diff** from step 5, reusing step 3, and never re-post a finding already answered. Stop when a round returns zero confirmed findings or the cap is hit.

At the cap with findings remaining: stop fixing, report the approach concern to the user and ask for direction.

Done when a round is clean or the cap triggered an escalation.

### 7. Report

Open with the count: "N confirmed findings fixed in R rounds", or "nothing needs fixing". List `pre-existing` findings and unanswered items separately. Name what the checks would miss, and hand that part to a human reviewer.

Recurring findings: propose a line for `REVIEW.md`, `CLAUDE.md`, or a lint rule so the next review never needs to find it.
