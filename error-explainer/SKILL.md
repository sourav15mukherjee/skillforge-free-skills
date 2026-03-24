---
name: error-explainer
description: >-
  Diagnose and fix error messages and stack traces. Use when the user pastes an
  error, stack trace, build failure, or runtime exception. Provides plain-English
  explanation, root cause analysis, and a working fix using actual project context.
version: "1.0.0"
tools:
  - Bash
  - Read
  - Grep
  - Glob
---

## Error Explainer

Diagnose any error message and provide a clear explanation with a working fix.

## Workflow

1. **Parse the error**
   Extract from the error message:
   - Error type/name (TypeError, SyntaxError, ENOENT, etc.)
   - Error message text
   - File path and line number (if in stack trace)
   - Framework context (React, Next.js, Node, webpack, etc.)

2. **Read the source**
   If a file and line number are referenced, read 20 lines of context around the error location.

3. **Identify root cause**
   Common patterns:
   - `Cannot read properties of undefined` → trace the variable back to where it becomes undefined
   - `Module not found` → check import paths, package.json, tsconfig paths
   - `Hydration mismatch` → find server/client rendering differences
   - `ENOENT` → verify the expected file path exists
   - Type errors → check type definitions and function signatures

4. **Search for related patterns**
   ```bash
   # Find similar usage that works correctly
   grep -rn "theFunction\|theVariable" --include="*.ts" --include="*.tsx" -l
   ```

5. **Explain in plain English**
   Structure the response:

   **What happened:** One-sentence summary anyone can understand.

   **Why it happened:** The specific chain of events that caused this error.

   **The fix:** Exact code change with before/after.

   **Prevention:** How to avoid this in the future (type guard, null check, etc.)

6. **Apply the fix**
   If the user confirms, edit the file directly. If multiple possible fixes exist, present options.

## Rules

- Always read the actual source file before suggesting a fix — never guess
- Explain errors in plain language, not jargon
- If you can't determine the exact cause from the error alone, ask the user for the file content or to reproduce it
- For build errors, check package.json and config files first
- For type errors, check tsconfig.json and type definitions
- Never suggest `any` as a TypeScript fix — find the real type
- If the error is from a dependency, check if the version matches the docs

## Example

**User pastes:**
```
TypeError: Cannot read properties of undefined (reading 'map')
    at UserList (src/components/UserList.tsx:14:22)
```

**Response:**
**What happened:** You're calling `.map()` on a variable that is `undefined` on line 14 of UserList.tsx.

**Why:** The `users` prop (or state) hasn't loaded yet — on first render it's `undefined`, and `.map()` can't be called on `undefined`.

**The fix:**
```typescript
// Before
{users.map(user => <UserCard key={user.id} user={user} />)}

// After
{users?.map(user => <UserCard key={user.id} user={user} />) ?? null}
```

**Prevention:** Initialize state with an empty array: `const [users, setUsers] = useState<User[]>([])`
