---
name: changelog-generator
description: >-
  Generate a CHANGELOG.md entry from git history. Use when the user asks to
  create a changelog, write release notes from commits, update CHANGELOG.md,
  or prepare for a release. Analyzes commits since the last tag and produces
  a formatted entry grouped by type.
version: "1.0.0"
tools:
  - Bash
  - Read
  - Write
---

## Changelog Generator

Generate a polished CHANGELOG.md entry from git commit history.

## Workflow

1. **Find the version range**
   ```bash
   # Get the latest tag
   git describe --tags --abbrev=0 2>/dev/null || echo "no-tags"
   # Get current branch
   git branch --show-current
   ```
   If no tags exist, use the full history. If the user specifies a range, use that.

2. **Collect commits**
   ```bash
   git log v1.0.0..HEAD --pretty=format:"%H|%s|%an|%ad" --date=short
   ```
   Parse each commit into: hash, subject, author, date.

3. **Categorize commits**
   Group by Conventional Commits prefix:
   - **Added** — `feat:` commits
   - **Fixed** — `fix:` commits
   - **Changed** — `refactor:`, `perf:` commits
   - **Security** — `security:` or security-related commits
   - **Deprecated** — commits mentioning deprecation
   - **Removed** — commits removing features
   - **Breaking Changes** — commits with `BREAKING CHANGE:` or `!:`

   Skip: merge commits, `chore:`, `style:`, `ci:` (unless significant).

4. **Check for existing CHANGELOG.md**
   If it exists, read the format and match it.
   If not, create one with the Keep a Changelog header.

5. **Generate the entry**
   Format:
   ```markdown
   ## [1.1.0] - 2026-03-25

   ### Added
   - Add OAuth2 Google login flow ([a1b2c3d])
   - Add user profile settings page ([d4e5f6a])

   ### Fixed
   - Fix session timeout not refreshing token ([b7c8d9e])
   - Fix mobile nav overlay z-index issue ([f0a1b2c])

   ### Breaking Changes
   - Remove deprecated /api/v1 endpoints — use /api/v2 ([c3d4e5f])
   ```

6. **Determine version number**
   Suggest based on changes:
   - Has breaking changes → major bump
   - Has new features → minor bump
   - Only fixes → patch bump

7. **Write the changelog**
   Prepend the new entry to CHANGELOG.md (after the header, before previous entries).

## Rules

- Always base the changelog on actual git commits — never fabricate entries
- Skip merge commits and trivial chores unless they're user-facing
- Link commit hashes as short refs
- Highlight breaking changes prominently at the top
- Use Keep a Changelog format: https://keepachangelog.com
- If commits don't follow Conventional Commits, do your best to categorize by reading the subject
- Ask the user for the version number if it can't be determined
