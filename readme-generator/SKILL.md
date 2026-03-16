---
name: readme-generator
description: >-
  Analyze project structure and generate a comprehensive README.md. Use when
  the user asks to create a README, generate documentation, write project docs,
  or wants a README for their repo. Scans the codebase and produces a polished
  README with all standard sections.
version: "1.0.0"
tools:
  - Bash
  - Read
  - Write
  - Grep
  - Glob
---

## README Generator

Analyze a project's structure, tech stack, and code to generate a comprehensive, well-organized README.md.

## Workflow

1. **Scan the project structure**
   ```bash
   find . -maxdepth 3 -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/dist/*" | head -80
   ```
   Identify the project type: Library, CLI Tool, Web Application, API Service, or Monorepo.

2. **Detect the tech stack**
   Read config files (`package.json`, `tsconfig.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `Dockerfile`).
   Extract: language, framework, test runner, linter, bundler, deployment target.

3. **Identify key entry points and exports**
   Read main source files to understand what the project does — exported functions, CLI commands, API endpoints.

4. **Extract existing documentation hints**
   Look for JSDoc/docstrings, existing partial README, CHANGELOG, LICENSE, CONTRIBUTING.

5. **Generate the README** with these sections:
   - **Title and Badges** (license, language, version, CI status)
   - **Description** (2-3 sentences)
   - **Features** (bulleted key capabilities)
   - **Prerequisites** (runtime versions, required accounts)
   - **Installation** (actual commands from package.json scripts)
   - **Configuration** (environment variables, config files)
   - **Usage** (real code examples matching the actual API)
   - **API Reference** (if applicable — table of endpoints/functions)
   - **Project Structure** (key directories tree)
   - **Scripts** (table of npm/make commands)
   - **Contributing** (dev setup, testing, PR process)
   - **License** (type with link)

6. **Write the README**
   If a README exists, ask to replace or merge.

## Rules

- Always read actual source code — never guess at what the project does
- Match README complexity to project size
- Use real values (actual script names, actual dependencies, actual ports)
- Include working code examples that reflect the actual API
- If no tests exist, suggest adding them in Contributing
- Keep tone professional but approachable
- Do not include irrelevant sections (no API Reference for a CLI tool)
