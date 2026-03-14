---
name: commit-message-generator
description: >-
  Generate conventional commit messages from staged git changes. Use when the
  user asks to commit, create a commit message, write a commit, or wants help
  with git commits. Analyzes staged diffs and produces structured messages
  following the Conventional Commits specification.
version: "1.0.0"
tools:
  - Bash
  - Read
---

## Commit Message Generator

Generate well-structured conventional commit messages by analyzing staged git changes.

## Workflow

1. **Check for staged changes**
   Run `git diff --cached --stat` to see what's staged. If nothing is staged, tell the user and suggest they stage files first.

2. **Analyze the diff**
   Run `git diff --cached` to get the full diff. Read through each file's changes carefully.

3. **Determine the commit type**
   Based on the changes, select the appropriate type:
   - `feat`: New feature or capability
   - `fix`: Bug fix
   - `refactor`: Code restructuring without behavior change
   - `docs`: Documentation only
   - `test`: Adding or updating tests
   - `chore`: Build, CI, dependencies, tooling
   - `style`: Formatting, whitespace, semicolons
   - `perf`: Performance improvement

4. **Determine scope (optional)**
   If the changes are localized to a specific module, component, or area, include a scope: `feat(auth): ...`

5. **Write the subject line**
   - Use imperative mood: "add" not "added" or "adds"
   - Keep under 72 characters
   - Don't end with a period
   - Be specific: "add OAuth2 Google login flow" not "update auth"

6. **Write the body (if needed)**
   For non-trivial changes, add a body separated by a blank line:
   - Explain WHY, not what (the diff shows what)
   - Wrap at 72 characters
   - Use bullet points for multiple reasons

7. **Check for breaking changes**
   If the change breaks backward compatibility, add:
   ```
   BREAKING CHANGE: description of what breaks and migration path
   ```

8. **Present the commit message**
   Show the full message to the user and ask for confirmation before committing.

## Rules

- Never skip the diff analysis — always base the message on actual changes
- If changes span multiple unrelated concerns, suggest splitting into separate commits
- Always use imperative mood in the subject
- Subject line must be under 72 characters
- Use a HEREDOC for the commit command to preserve formatting
- Include Co-Authored-By if the user requests it

## Example Output

```
feat(auth): add OAuth2 Google login flow

Implement Google OAuth2 authentication using Supabase Auth provider.
Users can now sign in with their Google account in addition to email
magic links.

- Add Google provider configuration in Supabase dashboard
- Create OAuth callback handler at /auth/callback
- Update login page with Google sign-in button
```
