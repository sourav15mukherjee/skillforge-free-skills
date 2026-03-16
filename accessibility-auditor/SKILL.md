---
name: accessibility-auditor
description: >-
  Scan web application code for WCAG 2.1 accessibility violations. Use when
  the user asks to check accessibility, audit a11y, find WCAG issues, review
  for screen reader support, or improve accessibility. Analyzes components
  and templates for violations and outputs a prioritized report.
version: "1.0.0"
tools:
  - Read
  - Grep
  - Glob
  - Bash
---

## Accessibility Auditor

Scan web application source code for WCAG 2.1 accessibility violations and produce a prioritized remediation report.

## Workflow

1. **Discover UI components and templates**
   Find all files containing HTML, JSX, TSX, Vue, Svelte:
   ```bash
   find . -maxdepth 5 \( -name "*.tsx" -o -name "*.jsx" -o -name "*.vue" -o -name "*.svelte" -o -name "*.html" \) -not -path "*/node_modules/*" -not -path "*/.next/*" | head -100
   ```
   Prioritize: layouts, navigation, forms, interactive widgets, media components.

2. **Check images and media (WCAG 1.1.1)**
   - `<img>` without `alt` — **Critical**
   - Icon-only buttons without accessible labels — **Critical**
   - SVG without `role="img"` and `aria-label` — **Major**
   - `<video>`/`<audio>` without captions — **Major**

3. **Check semantic HTML (WCAG 1.3.1)**
   - `<div onClick>` instead of `<button>` — **Critical**
   - Missing landmark elements (`<nav>`, `<main>`, `<aside>`)
   - Heading levels that skip (h1 → h3)
   - Inputs without associated `<label>`

4. **Check ARIA usage (WCAG 4.1.2)**
   - Interactive elements without accessible names
   - `aria-hidden="true"` on focusable elements — **Critical**
   - Missing roles on custom widgets (tabs, dialogs, accordions)
   - Missing state attributes (`aria-expanded`, `aria-selected`)

5. **Check keyboard navigation (WCAG 2.1.1)**
   - Custom controls without `tabIndex`
   - Missing `onKeyDown` handlers on custom interactive elements
   - `outline: none` without replacement focus style
   - Keyboard traps in modals

6. **Check focus management (WCAG 2.4.3)**
   - Modals without focus trapping
   - Missing skip navigation link
   - Route changes without focus management

7. **Check color contrast (WCAG 1.4.3)**
   - Low-contrast text/background combinations
   - Information conveyed by color alone
   - Common Tailwind pitfalls: `text-gray-400 on bg-white` = 2.7:1 (fails AA)

8. **Check forms (WCAG 3.3.1)**
   - Inputs without labels
   - Placeholder as only label — **Major**
   - Error messages not linked via `aria-describedby`

9. **Generate the audit report** by severity:
   **Critical** — Blocks assistive technology access
   **Major** — Significant barriers
   **Minor** — Improvement opportunities

   Each finding: file:line, WCAG criterion, current code, fixed code, who is affected.

## Rules

- Always reference specific WCAG 2.1 success criteria by number
- Provide before/after code snippets for every finding
- Distinguish decorative images (alt="" is correct) from informative ones
- Check both JSX and CSS files
- Prioritize critical issues over minor improvements
- Note which user groups are affected per finding
