---
name: triage-pr-feedback
description: Read all reviews, review comments, and conversation comments on a GitHub PR by number via the gh CLI, then group the feedback into categories and prioritize it into an action list. Triage report only — do not post replies or change code. Use when the user wants to triage, summarize, or get an action plan for PR feedback.
---

# Inputs

Expected from the user:

- a **PR number** (required), e.g. `123`
- optional focus, such as "only blocking items", "ignore nitpicks", "what's still unresolved"

# High-level behavior

Follow this order:

1. Resolve `owner/repo` (`gh repo view --json nameWithOwner -q .nameWithOwner`).
2. Fetch PR metadata, then **all three feedback streams plus resolved-thread status** (see [Fetching feedback](#fetching-feedback)).
3. Normalize every entry to: author, stream, body, file/line (if any), created time, resolved/outdated flag, contained `suggestion` blocks.
4. Categorize each actionable entry (see [Categories](#categories)). Exclude resolved/outdated from the action list but report the count.
5. Assign a priority to each entry (see [Prioritization](#prioritization)).
6. Present the report (see [Triage report](#triage-report)), leading with critical/high items.

# Fetching feedback

GitHub splits PR feedback across streams that `gh pr view` alone does **not** fully expose. Pull each:

````sh
# PR metadata + review summaries (state: APPROVED / CHANGES_REQUESTED / COMMENTED)
gh pr view <number> --json number,title,state,isDraft,author,reviews,body

# Inline review comments (code line comments, incl. ```suggestion blocks)
gh api repos/{owner}/{repo}/pulls/<number>/comments --paginate

# General conversation comments (not attached to code)
gh api repos/{owner}/{repo}/issues/<number>/comments --paginate
````

Resolved/outdated detection:

- An inline comment with `"position": null` is **outdated** (its line no longer exists in the diff).
- Thread resolution is only in GraphQL. Use it to flag resolved threads:

```sh
gh api graphql -f query='query($owner:String!,$repo:String!,$pr:Int!){
  repository(owner:$owner,name:$repo){ pullRequest(number:$pr){
    reviewThreads(first:100){ nodes{ isResolved isOutdated
      comments(first:1){ nodes{ body author{login} } } } } } } }' \
  -F owner=<owner> -F repo=<repo> -F pr=<number>
```

# Categories

Group each actionable entry into exactly one:

- **Critical / blocking** — must fix to merge; reviewer requested changes, security/data-loss/breakage concerns.
- **Bug / correctness** — a concrete defect or logic error, even if not flagged blocking.
- **Question** — reviewer needs an answer or clarification before resolving.
- **Hint / suggestion** — recommended improvement worth doing, not strictly required.
- **Nitpick / style** — optional polish: naming, formatting, wording.
- **Out-of-scope / follow-up** — valid but belongs in a separate PR or issue.

Reported separately, excluded from the action list:

- **Resolved / outdated** — resolved threads or comments on stale diff lines. Report the count so nothing looks dropped.

When a comment fits two categories, pick the higher-impact one. A `CHANGES_REQUESTED` review with no specifics still counts as a critical signal.

# Prioritization

- **P0** — Critical / blocking.
- **P1** — Bugs/correctness, and any item from a reviewer who requested changes.
- **P2** — Questions, hints.
- **P3** — Nitpicks.
- **Defer** — Out-of-scope / follow-up.

Within a priority, rank by: repeated by multiple reviewers > maintainer/code-owner author > recency.

# Triage report

Present, in this order:

1. **Header** — `#<number> <title>` · state · draft? · review verdicts (e.g. "2 approved, 1 changes-requested").
2. **Action list** — P0 then P1, each as: priority · category · `file:line` (if inline) · author · one-line summary · quoted `suggestion` if present.
3. **Lower priority** — P2/P3 collapsed/brief.
4. **Deferred & resolved** — counts plus one-line list of follow-up items.
5. **Recommendation** — the 1–5 things to address first, in order.

Quote reviewers' own words for the summary; do not invent feedback. If a stream is empty, say so rather than omitting it.

# Gotchas

- **Missing inline comments:** `gh pr view --comments` omits much inline review detail — always hit the `/pulls/.../comments` API.
- **Pagination:** PRs with heavy discussion need `--paginate`, or later comments are silently lost.
- **Bot noise:** Collapse CI/coverage/dependabot comments into a single noted line unless they contain a real finding.
- **Suggestion blocks:** Preserve ```suggestion content verbatim — it's directly appliable and high-signal.
- **No feedback yet:** If there are zero comments and reviews, say the PR is unreviewed instead of producing an empty report.
- **Fork PRs:** `<owner>/<repo>` is the base repo; confirm if the remote is a fork.
