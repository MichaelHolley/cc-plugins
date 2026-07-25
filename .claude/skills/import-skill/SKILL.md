---
name: import-skill
description: Discover a skill inside a GitHub repository and pin it as a git-subdir entry in this marketplace's `.claude-plugin/marketplace.json`, or re-pin an existing entry to a newer sha. Use when the user wants to import, add, link, update, bump, or re-pin an external skill by repo owner, repo name, and skill name.
argument-hint: <owner> <repo> [skill-name]
---

# Inputs

Expected from the user:

- **repo owner** (required), e.g. `mattpocock`
- **repo name** (required), e.g. `skills`
- **skill name** (optional) — the skill's directory name, e.g. `grilling`. Without it, list the repo's skills and ask which to import.

Intent is either **link** (add a new entry) or **update** (re-pin an existing entry to a newer sha). Infer it from whether the skill already appears in `.claude-plugin/marketplace.json`; ask only if the user's words contradict what you find.

# High-level behavior

1. Read `.claude-plugin/marketplace.json` and check whether the skill already has an entry. This decides link vs update.
2. Resolve the repo's default branch and its HEAD sha (see [Discovery](#discovery)).
3. Locate the skill's `SKILL.md` at that sha. Every candidate path must be confirmed to exist at the resolved sha — never a path from memory or from an existing entry.
4. Read the skill's frontmatter `name` and `description` at that sha.
5. Resolve the author's display name.
6. Write the entry (see [Entry shape](#entry-shape)) into the `plugins` array, then verify the file still parses as JSON.
7. Report: name, path, sha (short), and — on update — the old sha it moved from.

Done when the entry exists with a full 40-character sha, its `path` is confirmed present at that sha, and the JSON parses.

# Discovery

`?` needs quoting in zsh — quote every URL that carries a query string.

```sh
# Default branch + HEAD sha (the pin)
gh api repos/<owner>/<repo> --jq '.default_branch'
gh api repos/<owner>/<repo>/commits/<branch> --jq '.sha'

# Every skill in the repo, at that exact sha
gh api "repos/<owner>/<repo>/git/trees/<sha>?recursive=1" \
  --jq '.tree[] | select(.path|endswith("SKILL.md")) | .path'

# The skill's frontmatter, at that exact sha
gh api "repos/<owner>/<repo>/contents/<path>/SKILL.md?ref=<sha>" --jq '.content' | base64 -d

# Author display name (falls back to the login when null)
gh api users/<owner> --jq '.name'
```

Matching the skill name: prefer an exact directory-name match on the segment before `SKILL.md`. On several matches or none, show the candidate paths and ask.

# Entry shape

Append to `plugins`, matching the surrounding entries:

```json
{
  "name": "<skill-name>",
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/<owner>/<repo>.git",
    "path": "<dir containing SKILL.md>",
    "sha": "<40-char commit sha>"
  },
  "description": "<what the skill does>",
  "author": {
    "name": "<display name>"
  }
}
```

- **`path`** is the directory, not the `SKILL.md` file.
- **`sha`** is a full 40-character commit sha, never a branch name or short sha.
- **`description`** is human-facing: take the skill's frontmatter description and strip the trigger phrasing ("Use when…", "invokes /…"), keeping one sentence on what it does.
- **`name`** may differ from the directory name if the user asks; otherwise use the directory name.

On update, change only `sha` — and `path`, if the skill moved. Leave a hand-edited `name` or `description` alone unless the user asks or the upstream description materially changed.

# Gotchas

- **Skill deleted or moved upstream:** the pinned path may not exist at HEAD (this is why `caveman` sits on an older sha). Do not bump a pin to a sha where the path is gone — report it and ask whether to re-point at the new path or hold the old pin.
- **Already at HEAD:** say so and change nothing rather than rewriting an identical entry.
- **Truncated tree:** if the tree response has `"truncated": true`, the listing is incomplete — fall back to `gh api "repos/<owner>/<repo>/contents/<dir>?ref=<sha>"` to walk directories.
- **Name collision:** a marketplace `name` must be unique across `plugins`. On collision, ask for a distinguishing name instead of overwriting.
- **Multi-skill repos:** one entry per skill directory. A repo exposing four skills gets four entries (see the `ponytail` entries), not one pointing at the repo root.
- **`source: github` entries:** the shorthand form (like `humanizer`) carries no sha. Converting one to a pinned `git-subdir` entry needs the user's go-ahead.
- **Private or missing repo:** `gh` returns 404 for both. Report it as "not found or no access" rather than guessing a path.
