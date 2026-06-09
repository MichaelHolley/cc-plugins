---
name: review-with-intent
description: Review a PR, branch, or set of changes against a user-provided intent. Extracts structured intent and acceptance criteria from a GitHub issue number, pasted description, or free-form text, then compares the actual implementation against that intent. Highlights matches, divergences, gaps, and basic quality. Use when the user wants to validate that code changes fulfill a specific goal, ticket, or requirement, or when reviewing a PR against its stated purpose.
---

# Goal

Validate that code changes actually do what they are supposed to do. Extracts the intended behavior from a user-provided source (GitHub issue, pasted spec, free-form description), then reviews the diff against that intent — surfacing where the code matches, where it diverges, what is missing, and whether the implementation quality is sound.

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

If intent source is missing, ask for it before proceeding.

# Tools and environment assumptions

- Current working directory is a local clone of the target repository
- `gh` is installed and authenticated (for fetching GitHub issues or PR diffs)
- `git` is available

# High-level behavior

Follow this approach strictly in order:

## Step 1 — Extract intent (subagent)

Spawn a subagent to extract the structured intent from the provided source.

The subagent must produce:

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

Produce a structured report (see format below). Keep it concise. Do not pad.

# Report format

```
## Intent summary
<one-sentence goal from Step 1>

## Acceptance criteria
- [ ] <criterion> — <MET / PARTIALLY MET / NOT MET>
- [ ] ...

## Matches
- <what the implementation gets right relative to the intent>

## Gaps
- <what is missing or unaddressed>

## Divergences
- <where the implementation contradicts or misinterprets the intent>

## Scope creep
- <changes outside the stated scope, if any>

## Implementation quality
- <notable quality observations — positive or negative>

## Verdict
<one or two sentences: does this implementation fulfill the intent? What is the most important thing to address?>
```

Omit any section that is empty (e.g. no divergences → omit that section).

# Gotchas

- **No intent provided:** Ask before doing anything else. Do not guess the intent from the code.
- **Closed or missing issue:** Warn the user and ask whether to continue with whatever body is available.
- **Large diff:** Focus on files most relevant to the stated intent. Note if coverage was partial.
- **No tests:** Call it out under implementation quality, but do not block the review.
- **Ambiguous intent:** Surface the ambiguity in the report rather than silently resolving it.
- **PR not yet open:** Fall back to branch diff; note that no PR description was available.
