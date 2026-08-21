---
name: matches-intent
description: Validate a implementation against its original intent. Use when the user wants to check whether a PR, branch, or diff actually does what it was intended to do.
---

# Inputs

Expected input from the user:

- **Intent source** — one of:
  - a GitHub issue number (e.g. `#42`)
  - a GitHub issue URL
  - pasted text (spec, ticket body, acceptance criteria, user story)
  - free-form description of the goal
- **Changes to review** — one of:
  - current branch diff vs main (default)
  - a specific PR number or URL
  - a named branch
  - an explicit commit range

If intent source is missing but a PR is provided, check the PR description for linked issues (e.g. `Closes #42`, `Fixes #42`, bare `#42` references) before asking — fetch the linked issue and use it as the intent source. Only ask if no intent can be found this way.

# High-level behavior

Follow this approach strictly in order:

## Step 1 — Extract intent

Extract the structured intent from the provided source. Produce:

- **Goal** — one-sentence summary of the intent
- **Scope** — what is in and out of scope (inferred if not explicit)
- **Acceptance criteria** — bulleted list; extract explicit ones or derive from the description
- **Key behaviors** — specific behaviors or constraints the implementation must satisfy

## Step 2 — Gather the diff

Get the code changes with `gh` cli and git operations.

## Step 3 — Review against intent

Compare the diff to the structured intent from Step 1. Evaluate:

1. **Intent match** — which acceptance criteria and key behaviors are clearly implemented
2. **Intent gaps** — criteria or behaviors with no corresponding implementation
3. **Intent divergence** — implementation that contradicts or deviates from the stated intent
4. **Scope creep** — changes that fall outside the stated scope
5. **Implementation quality** — basic quality signals: error handling, naming, edge cases, test coverage, obvious bugs

Do NOT perform a full deep code review. Focus on whether the intent is met.

## Step 4 — Report

Produce a structured report following [REPORT_FORMAT.md](REPORT_FORMAT.md). Keep it concise. Do not pad.

# Gotchas

- **No intent provided:** Ask before doing anything else. Do not guess the intent from the code.
- **Closed or missing issue:** Warn the user and ask whether to continue with whatever body is available.
- **Large diff:** Focus on files most relevant to the stated intent. Note if coverage was partial.
- **No tests:** Call it out under implementation quality, but do not block the review.
- **Ambiguous intent:** Surface the ambiguity in the report rather than silently resolving it.
- **PR not yet open:** Fall back to branch diff; note that no PR description was available.
