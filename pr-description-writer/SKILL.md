---
name: pr-description-writer
description: >-
  Generate comprehensive pull request descriptions from branch diffs. Use when
  the user asks to create a PR, write a PR description, or prepare a pull
  request. Analyzes all commits and changes between branches to produce
  structured descriptions with summary, changes list, and test plan.
version: "1.0.0"
tools:
  - Bash
  - Read
  - Grep
---

## PR Description Writer

Generate detailed, well-structured pull request descriptions by analyzing branch diffs.

## Workflow

1. **Identify branches**
   Determine the current branch and the base branch (usually `main` or `master`):
   ```bash
   git branch --show-current
   git log --oneline main..HEAD
   ```

2. **Analyze all changes**
   Get the full diff and commit history:
   ```bash
   git diff main...HEAD --stat
   git diff main...HEAD
   git log main..HEAD --format="%h %s"
   ```

3. **Categorize changes**
   Group changes into logical categories:
   - New features
   - Bug fixes
   - Refactoring
   - Configuration changes
   - Test additions/updates
   - Documentation updates

4. **Generate the PR description** using this format:

```markdown
## Summary
[1-3 sentences explaining the purpose and motivation]

## Changes
### [Category 1]
- Specific change with file reference
- Another change

### [Category 2]
- Change details

## Test Plan
- [ ] Manual testing step 1
- [ ] Manual testing step 2
- [ ] Automated tests pass

## Notes for Reviewers
[Any context that helps reviewers, areas of concern, or deployment notes]
```

## Rules

- Always analyze ALL commits, not just the latest one
- Focus the summary on WHY, not WHAT
- Reference specific files when describing changes
- Include both manual and automated test steps
- Flag any breaking changes or migration requirements
- Keep the description scannable — use bullet points and headers
- If the PR is too large (>500 lines), suggest splitting it
