---
name: gone-cleanup
description: Delete local git branches whose remote branch is gone, together with their leftover worktrees, after per-branch confirmation. Use when the user wants to clean up stale, merged, or "gone" local branches or worktrees.
disable-model-invocation: true
---

# Gone cleanup

## Workflow

### 1. List gone branches and worktrees

Run `git fetch --prune`, then `git branch -vv` and `git worktree list --porcelain`.

- Pick out the branches marked `: gone]`. Ignore all others.
- For each, note whether it is checked out in a linked worktree and where.
- For each such worktree, run `git -C <path> status --porcelain` and flag uncommitted or untracked changes.
- Separately collect worktrees marked `prunable` (directory missing on disk).

Report everything to the user as a list: branch, last commit subject, worktree path and dirty flag if any. If there is nothing gone or prunable, say so and stop.

### 2. Ask per branch

Use AskUserQuestion with one question per gone branch (max 4 per call, batch the rest), options: `Delete` and `Keep`. Never preselect or assume `Delete`. For a branch with a worktree, the question says that deleting removes the worktree too, and names the dirty flag if set.

Skip and list as skipped instead of asking: the current branch, any branch checked out in the main worktree, and locked worktrees.

### 3. Confirm

Summarize the branches and worktrees chosen for deletion, plus any prunable worktree entries, and ask for a final confirmation with AskUserQuestion. Delete nothing without an explicit yes.

### 4. Delete

For each confirmed branch:

1. If it has a worktree, run `git worktree remove <path>`. If git refuses because of uncommitted changes, do not use `--force`. Report the changes and ask again before forcing.
2. Run `git branch -d <branch>`. If git refuses because the branch is not fully merged (common after squash merges), do not fall back to `-D`. Report it and ask again, naming the unmerged commits, before using `git branch -D`.

Finally run `git worktree prune` if prunable entries were confirmed.

Report which branches and worktrees were deleted, kept, or skipped.
