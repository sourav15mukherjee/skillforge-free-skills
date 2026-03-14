---
name: git-branch-manager
description: >-
  Create, manage, and clean up git branches with smart naming conventions.
  Use when the user asks to create a branch, switch branches, clean up
  branches, list branches, or manage git workflow. Follows team naming
  conventions and prevents common mistakes.
version: "1.0.0"
tools:
  - Bash
---

## Git Branch Manager

Create, manage, and clean up git branches with smart naming and safety checks.

## Workflow

### Creating Branches
1. Determine the branch type from user intent:
   - New feature → `feature/`
   - Bug fix → `bugfix/` or `fix/`
   - Hotfix → `hotfix/`
   - Release → `release/`
   - Experiment → `experiment/`

2. Generate the branch name:
   - Use kebab-case: `feature/add-oauth-login`
   - Include ticket number if mentioned: `feature/PROJ-123-add-oauth`
   - Keep concise but descriptive

3. Ensure clean state before creating:
   ```bash
   git status --porcelain
   ```
   If there are uncommitted changes, warn the user and suggest stashing.

4. Create from the right base:
   - Features → from `main` or `develop`
   - Hotfixes → from `main`
   - Fetch latest first: `git fetch origin`

### Listing Branches
Show branches with useful context:
```bash
git branch -a --sort=-committerdate --format='%(refname:short) %(committerdate:relative) %(subject)'
```

### Cleaning Up
1. Find merged branches: `git branch --merged main`
2. Find stale branches (no commits in 30+ days)
3. Show what would be deleted BEFORE deleting
4. Never delete `main`, `master`, `develop`, or the current branch
5. Use `-d` (safe delete) not `-D` (force delete)

## Rules

- Always fetch before creating branches
- Never force-delete branches without user confirmation
- Warn if branching from a non-default base
- Check for uncommitted changes before switching
- Show branch age and merge status when listing
