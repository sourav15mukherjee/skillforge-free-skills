---
name: env-config-manager
description: >-
  Manage .env files, validate required variables, generate .env.example
  templates, and detect missing config between environments. Use when the user
  asks to check environment variables, create an env example file, compare
  configs, validate env setup, or audit secrets in their repository.
version: "1.0.0"
tools:
  - Bash
  - Read
  - Write
  - Grep
  - Glob
---

## Environment Config Manager

Manage, validate, and synchronize environment configuration files across your project and deployment targets.

## Workflow

1. **Discover all environment files**
   Search the project for every env-related file:
   ```bash
   find . -maxdepth 3 -name ".env*" -not -path "*/node_modules/*" -not -path "*/.git/*"
   ```
   Common files: `.env`, `.env.local`, `.env.development`, `.env.staging`, `.env.production`, `.env.example`

2. **Parse and catalog all variables**
   For each env file, extract every variable name and note:
   - Which file defines it
   - Whether it has a non-empty value
   - Whether the key looks like a secret (contains KEY, SECRET, TOKEN, PASSWORD, CREDENTIAL, PRIVATE)
   - Whether it has a comment/description

3. **Validate required variables**
   Check that the active `.env` contains every variable from the template:
   ```
   Missing in .env (defined in .env.example):
     - DATABASE_URL
     - STRIPE_SECRET_KEY
   Extra in .env (not in .env.example):
     - DEBUG_MODE
   ```

4. **Detect variables referenced in code but missing from env files**
   ```bash
   grep -rn "process\.env\." --include="*.ts" --include="*.tsx" --include="*.js" src/ app/ | grep -oP 'process\.env\.\K[A-Z_][A-Z0-9_]*' | sort -u
   ```
   Compare against env files and report unreferenced variables.

5. **Generate or update .env.example**
   Create a sanitized template from the current `.env`:
   - Strip secret values, replace with descriptive placeholders
   - Preserve non-secret defaults (PORT=3000, NODE_ENV=development)
   - Group variables by prefix with section comments

6. **Compare environments**
   Produce a comparison matrix:
   ```
   Variable               .env    .env.staging  .env.production
   DATABASE_URL            ✓           ✓              ✓
   STRIPE_SECRET_KEY       ✓           ✓              ✗ ← MISSING
   DEBUG_MODE              ✓           ✗              ✗
   ```

7. **Audit git safety**
   ```bash
   grep -n "\.env" .gitignore 2>/dev/null
   git ls-files --cached | grep -i "\.env"
   ```
   Flag tracked .env files with secrets as **critical**.

8. **Report findings** organized by severity:
   - **Critical**: Secrets at risk of git exposure, production env missing required vars
   - **Warning**: Variables referenced in code but not in env files
   - **Info**: Extra variables not referenced in code

## Rules

- NEVER print actual values of secret environment variables
- Always treat variables containing KEY, SECRET, TOKEN, PASSWORD, PRIVATE, CREDENTIAL as sensitive
- When generating .env.example, replace secrets with placeholder hints
- Do not modify .env files without explicit user confirmation
- If .env is not in .gitignore, warn immediately
- Preserve comments and grouping when updating .env.example
