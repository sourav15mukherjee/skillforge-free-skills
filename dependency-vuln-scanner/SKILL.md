---
name: dependency-vuln-scanner
description: >-
  Scan project dependencies for known CVEs, outdated packages, and supply
  chain risks. Use when the user asks to audit dependencies, check for
  vulnerabilities, scan packages, run a security check on node_modules, or
  wants to know if their dependencies are safe. Supports npm, pip, cargo,
  go mod, maven, gem, and nuget.
version: "1.0.0"
tools:
  - Read
  - Bash
  - Grep
  - Glob
---

## Dependency Vulnerability Scanner

Audit project dependencies for security vulnerabilities, supply chain risks, and outdated packages with severity-rated findings and actionable remediation.

## Workflow

1. **Detect the project's ecosystem(s)**
   Search for dependency manifest files:
   - `package.json` → Node.js/npm/yarn/pnpm
   - `requirements.txt` or `pyproject.toml` or `Pipfile` → Python
   - `go.mod` → Go
   - `Cargo.toml` → Rust
   - `pom.xml` or `build.gradle` → Java/Kotlin
   - `Gemfile` → Ruby
   - `*.csproj` or `packages.lock.json` → .NET

   Read all found manifests. Note the lock file (package-lock.json, yarn.lock, etc.) existence.

2. **Pass 1 — Known CVE Scan**
   Run the ecosystem's built-in audit tool:
   - npm: `npm audit --json`
   - pip: `pip-audit` or `safety check`
   - cargo: `cargo audit`
   - go: `govulncheck ./...`
   - maven: `mvn dependency-check:check`

   If the built-in tool is not available, read the manifest and manually cross-reference packages against common CVE databases.

   For each vulnerability found:
   - Package name and version
   - CVE identifier
   - CVSS score (Critical ≥9.0, High ≥7.0, Medium ≥4.0, Low <4.0)
   - Whether it's in direct or transitive dependency
   - Fix available? (patched version, no fix, or workaround)

3. **Pass 2 — Supply Chain Risk Analysis**
   For each direct dependency, check:
   - **Maintainer activity**: Has the package been updated in the last 12 months?
   - **Download trends**: Is download count declining (potential abandonment)?
   - **Typosquatting risk**: Is the name suspiciously similar to a popular package?
   - **License compatibility**: Are there restrictive licenses (GPL-3.0, AGPL) in an MIT project?
   - **Dependency depth**: Does it pull in a massive transitive tree?

4. **Pass 3 — Outdated Packages**
   Run the ecosystem's outdated check:
   - npm: `npm outdated --json`
   - pip: `pip list --outdated`
   - cargo: `cargo outdated`

   Flag packages that are:
   - Multiple major versions behind (high risk of breaking changes)
   - End-of-life (no longer receiving security patches)

5. **Generate the report**
   Structure findings by severity:

   ## Dependency Security Report

   ### Critical (CVSS ≥ 9.0) — Fix Immediately
   ### High (CVSS 7.0–8.9) — Fix This Sprint
   ### Medium (CVSS 4.0–6.9) — Plan Fix
   ### Low (CVSS < 4.0) — Monitor

   ### Supply Chain Risks
   ### Outdated Packages

   For each finding include:
   - **Package**: name@version
   - **Issue**: Description of the vulnerability or risk
   - **Severity**: CVSS score or risk level
   - **Fix**: Exact command to remediate
   - **Impact**: What the fix might break (if major version upgrade)

6. **Provide remediation commands**
   Generate copy-pasteable fix commands:
   ```bash
   # Fix critical vulnerabilities
   npm audit fix

   # Upgrade specific package
   npm install package-name@patched-version

   # Force fix (may include breaking changes)
   npm audit fix --force
   ```

## Rules

- Always show CVE IDs for known vulnerabilities — don't describe them vaguely
- Distinguish between direct and transitive dependencies
- Never suggest a fix that would break the application without warning
- If a vulnerability has no fix, recommend a workaround or alternative package
- Include the scan timestamp in the report
- Respect semver: flag major version bumps as potentially breaking
- If the project has a lock file, run audit against it (not just manifest)
- Check for both runtime AND dev dependency vulnerabilities
- Don't ignore low-severity issues — they can compound in supply chain attacks

## Example Output

```
## Dependency Security Report
Scanned: 2026-04-03 | Ecosystem: Node.js (npm)
Direct dependencies: 24 | Transitive: 187

### Critical — Fix Immediately
1. **lodash@4.17.20** (direct)
   CVE-2021-23337 | CVSS 9.8
   Command injection via template()
   Fix: npm install lodash@4.17.21

### High — Fix This Sprint
2. **express@4.17.1** (direct)
   CVE-2024-29041 | CVSS 7.5
   Open redirect in express.static
   Fix: npm install express@4.21.0

### Supply Chain Risks
- **left-pad** — Last updated 4 years ago, single maintainer
  Consider pinning or replacing with native String.padStart()
```
