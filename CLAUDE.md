# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code **marketplace** plus the `dev-workflows` **plugin** it ships.

## Two layers, don't confuse them

1. **`.claude-plugin/plugin.json`** — the `dev-workflows` plugin manifest. Its skills are the directories in `skills/`, which are authored and versioned here.
2. **`.claude-plugin/marketplace.json`** — the marketplace listing. Its `plugins` array holds *both* `dev-workflows` (`"source": "./"`) and a set of **third-party skills vendored by reference** — `git-subdir` entries pinned to a full 40-char commit sha, plus `source: github` shorthand entries that carries no sha.

`.claude/skills/import-skill/` is a repo-local tool skill, not part of the published plugin. It is the correct way to add or re-pin a `git-subdir` entry — it enforces sha resolution and path-exists-at-sha checks. Read it before hand-editing `marketplace.json`.

## Authoring a skill in `skills/`

Each skill is `skills/<name>/SKILL.md` with YAML frontmatter:

- `name` — must match the directory name.
- `description` — one sentence on what it does, then the trigger phrasing (`Use when…`). This is the only thing the model sees when deciding to invoke, so it carries the full trigger surface. The description should not explain how it works and what it does in detail, but explain when the skill should be used and pulled into the session.
- `argument-hint` — only when the skill takes positional args (`<pr-number> [short|full]`).

## Versioning

Bump `version` in `.claude-plugin/plugin.json` when `skills/` changes materially.
Marketplace-only changes (adding or re-pinning a third-party entry) require a patch level bump.