---
name: rewrite-gh-issue
description: Use when the user wants an existing GitHub issue improved, clarified, tightened, normalized to the repo's template, or fully rewritten.
argument-hint: <issue-number>
---

# Inputs

Expected input from the user:

- a GitHub issue number
- optional intent, such as:
  - make this issue more actionable
  - rewrite this bug report
  - turn this into a proper feature request
  - align this with our issue template
  - clean up and update issue 123

# Issue template locations

- `.github/ISSUE_TEMPLATE/`
- `.github/issue_template/`
- `docs/`
- repository root for issue form or markdown templates

# High-level behavior

Follow this approach:

1. Validate the environment.
2. Fetch the issue by number using the GitHub CLI.
3. Inspect the current issue title, body, labels, and metadata.
4. Explore the codebase, docs, tests, and configuration related to the issue.
5. Try to answer unresolved questions from the repository before asking more.
6. Ask the user targeted clarifying questions in a “grilling” interview style.
7. Find a matching issue template if one exists.
8. Rewrite the title and body in a cleaner, more complete form.
9. Present the proposed rewritten issue to the user.
10. Ask for explicit confirmation.
11. Only after confirmation, update the issue title and body using `gh issue edit`.

# Gotchas

- **Closed issue:** Warn the user before doing any work. Ask whether to proceed.
- **Empty body:** Skip "preserve existing details" — treat it as a fresh write.
- **No template found:** Fall back to the rewrite requirements. Don't invent structure.
- **Fork:** Detect if the repo is a fork and ask which repo to target before editing.
- **Metadata:** `gh issue edit` only updates title and body. Note label/assignee suggestions but don't apply them.

# Rewrite requirements

The rewritten issue should:

- have a clearer, more specific title
- be concise but complete
- use repository-specific terminology where appropriate
- follow the matched issue template when applicable
- remove ambiguity
- make assumptions explicit
- avoid invented facts
- preserve important existing details unless they are clearly obsolete or misleading

Prefer including, where applicable:

- summary
- current behavior
- expected behavior
- reproduction steps
- scope
- technical context
- proposed direction
- acceptance criteria
- risks or constraints
- unanswered questions
