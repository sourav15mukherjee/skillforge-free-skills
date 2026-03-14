---
name: test-generator
description: >-
  Generate comprehensive unit and integration tests for functions, classes,
  or modules. Use when the user asks to write tests, add tests, create test
  coverage, or test a specific function. Detects the testing framework and
  produces thorough test suites with edge cases.
version: "1.0.0"
tools:
  - Bash
  - Read
  - Write
  - Grep
  - Glob
---

## Test Generator

Generate thorough test suites by analyzing source code and producing tests that cover happy paths, edge cases, and error scenarios.

## Workflow

1. **Detect the testing framework**
   Look for configuration files and existing tests:
   - `jest.config.*` or `vitest.config.*` → Jest/Vitest
   - `pytest.ini` or `conftest.py` → Pytest
   - `*_test.go` files → Go testing
   - `*.test.ts`, `*.spec.ts` → TypeScript testing

   Also check `package.json` for test scripts and dependencies.

2. **Read the source code**
   Read the file(s) the user wants tested. Understand:
   - Function signatures and return types
   - Dependencies and imports
   - Side effects (API calls, DB queries, file I/O)
   - Edge cases (null inputs, empty arrays, boundary values)

3. **Read existing tests** (if any)
   Check for existing test files to match style and avoid duplication.

4. **Generate tests** covering:
   - **Happy path**: Normal expected behavior with typical inputs
   - **Edge cases**: Empty inputs, null/undefined, boundary values, large inputs
   - **Error handling**: Invalid inputs, thrown exceptions, rejected promises
   - **Integration points**: Mock external dependencies appropriately

5. **Write the test file**
   Follow the project's naming convention (e.g., `foo.test.ts` or `test_foo.py`).

## Rules

- Match the existing test style in the project
- Use descriptive test names: `it("should return empty array when input is null")`
- Group related tests with `describe` blocks
- Mock external dependencies (APIs, databases) — never call real services
- Include setup/teardown when needed
- Aim for 80%+ code coverage of the target file
- Add comments explaining WHY a test exists for non-obvious cases
- Always run the tests after generating to verify they pass
