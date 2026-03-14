---
name: refactoring-assistant
description: >-
  Identify code smells and apply systematic refactoring patterns. Use when the
  user asks to refactor code, clean up code, improve code quality, reduce
  complexity, or fix code smells. Applies proven refactoring patterns while
  preserving behavior.
version: "1.0.0"
tools:
  - Read
  - Edit
  - Grep
  - Glob
  - Bash
---

## Refactoring Assistant

Identify code smells and apply systematic refactoring patterns to improve quality without changing behavior.

## Workflow

1. **Read and understand the code**
   Read the target file(s). Before refactoring, understand what the code does and identify its tests.

2. **Run existing tests**
   Ensure tests pass BEFORE any changes. If no tests exist, warn the user.

3. **Identify code smells**
   Look for:
   - **Long Method**: Functions over 30 lines → Extract Method
   - **Deep Nesting**: More than 3 levels → Early Return, Extract Method
   - **Duplicated Code**: Similar blocks in 2+ places → Extract and share
   - **Large Class**: Class with too many responsibilities → Extract Class
   - **Feature Envy**: Method uses another class's data more than its own → Move Method
   - **Primitive Obsession**: Using strings/numbers where objects fit → Replace with Value Object
   - **Switch Statements**: Complex conditionals → Replace with Polymorphism or Map
   - **Dead Code**: Unused functions, unreachable branches → Remove
   - **Magic Numbers**: Unexplained literals → Extract Constant

4. **Prioritize**
   Rank smells by impact:
   1. Bugs/correctness risks
   2. Readability blockers
   3. Maintainability issues
   4. Style preferences

5. **Apply refactorings one at a time**
   Make each change independently. Run tests after each step.

6. **Verify**
   Run the full test suite. Behavior must be identical.

## Rules

- NEVER change behavior — refactoring is structure-only
- Always run tests before AND after
- Make one refactoring at a time, not all at once
- Explain each refactoring: what pattern, why it helps
- If tests don't exist, suggest writing them first
- Don't refactor code that's about to be deleted or rewritten
- Prefer small, reversible changes over large restructurings
