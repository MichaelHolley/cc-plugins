---
name: grill
description: Stress-test a plan, design, or decision by interviewing the user one round of pickable questions at a time. Use when the user wants their thinking challenged, gaps found, or assumptions surfaced before work starts, or says "grill me" or "grilling".
---

# Grill

Interview the user until you both understand the plan the same way. Model it as a **design tree**: every decision branches into the decisions hanging off it.

Ask through the AskUserQuestion tool, never as prose questions in your reply.

## Rounds

The **frontier** is every decision whose prerequisites are already settled — what you can ask now without guessing at answers you haven't heard. Work the frontier in rounds:

1. Compute the frontier.
2. Ask up to 4 of its questions in one AskUserQuestion call. If the frontier is wider than 4, ask the ones that branch the most first.
3. The answers settle those decisions and unblock what depended on them. Recompute and repeat.

A question whose answer depends on another question still open in this round belongs to a later round.

## Question shape

Each AskUserQuestion question:

- `header` — 1-2 words naming the decision.
- `question` — the decision, plus whatever context the user needs to pick fast. Name the tradeoff.
- `options` — 2-4 real, mutually exclusive answers. Put your recommendation first and append " (Recommended)" to its label. Every option's `description` says what happens if it's picked.
- `multiSelect: true` only when the answers genuinely stack.
- `preview` when the choice is about concrete shape — layout, API signature, config, diagram. Single-select only.

Never offer an "Other" option; the tool adds one.

## Facts are your job

When a question needs a fact from the environment (files, git, deps, APIs), find it yourself. Don't ask the user anything you can look up.
Don't block on lookups. A running exploration is an unsettled prerequisite, so only questions downstream of it wait. Ask the rest of the frontier now.

## Done

The session is done when the frontier is empty: every branch of the design tree visited, nothing left silently assumed. Do not act on it until the user confirms you have reached a shared understanding.
