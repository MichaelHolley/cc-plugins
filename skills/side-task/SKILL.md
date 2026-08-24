---
name: side-task
description: Park an out-of-scope task as a one-PR GitHub issue labelled for an agent to pick up. Use when something surfaces mid-work that should be captured now and done later.
---

# Side Task

The whole output of this skill is one parked issue: an agent picks it up later, on its own branch, and closes it with a single PR. The work in progress stays exactly as it is.

## One-PR sized

A task is **one-PR sized** when it is one branch, one PR, and no open design questions. Larger — it needs a decision from the user, spans unrelated areas, or has no clear finish line — and it belongs in a plain issue instead.

## Workflow

### 1. State the task

Take the user's description. When they point at a file, symbol, or diff instead, read enough of the repo to state the task yourself. Ask a question only when the issue would be unactionable without the answer, and ask at most one. **Done when** the task fits in one sentence.

### 2. Size it

Check the task is one-PR sized. When it is not, say which part fails and offer to file a plain issue. **Done when** the task is one-PR sized, or the user has taken the plain-issue path.

### 3. Write the issue

Title: one short line naming the outcome.

Body, in this order:

- **Goal** — one or two sentences on what is true once the PR lands.
- **Where** — the repo-relative paths involved.
- **Acceptance criteria** — a checklist the picking-up agent can verify itself.
- **Non-goals** — what to leave alone, so the PR stays small.

Every line traces to something the user said or something you read in the repo. Use the repo's own terminology. When the repo has an issue template, follow it and place these four parts inside its sections. **Done when** all four parts are written and every line traces to the user or the repo.

### 4. File it

Show the title and body and confirm with the user. Create the `agent-task` label if the repo lacks it, then create the issue with that label. Report the URL in one line and pick the interrupted work back up. **Done when** the issue exists and its URL is reported.
