---
name: i18n-helper
description: >-
  Extract hardcoded strings into translation keys, generate locale files,
  find untranslated strings, and validate locale completeness. Use when the
  user asks to internationalize their app, add translations, extract strings,
  check for missing translations, or set up i18n.
version: "1.0.0"
tools:
  - Read
  - Write
  - Grep
  - Glob
  - Bash
---

## i18n/Localization Helper

Extract hardcoded strings, generate locale files, and validate translation completeness across languages.

## Workflow

1. **Detect the i18n framework in use**
   ```bash
   grep -E "i18next|react-intl|vue-i18n|next-intl|@formatjs" package.json 2>/dev/null
   ```
   Check for config files: `i18n.ts`, `next.config.js` with i18n key, `src/locales/`.
   If no framework found, recommend one and help set it up.

2. **Discover existing locale files**
   ```bash
   find . -maxdepth 4 \( -name "*.json" -o -name "*.yaml" \) -path "*/locale*" -o -path "*/i18n/*" -o -path "*/translations/*" | grep -v node_modules
   ```
   Parse each file, build key inventory per language. Note structure: flat vs nested keys.

3. **Scan source code for hardcoded strings**
   Find user-facing strings in JSX text, component props, toast/alert calls, page titles, form validation messages.
   Exclude: CSS classes, console.log, import paths, test strings, URLs.

4. **Generate translation keys**
   Naming convention: `page.section.element`
   ```
   "Welcome back"     → common.welcomeBack
   "Submit"           → common.buttons.submit
   "Email is required" → forms.validation.emailRequired
   ```

5. **Extract and update source code**
   Replace hardcoded strings with translation calls:
   ```tsx
   // Before
   <h1>Welcome back</h1>

   // After (i18next)
   const { t } = useTranslation();
   <h1>{t("common.welcomeBack")}</h1>
   ```

6. **Generate or update locale files**
   Create JSON/YAML for each language. Non-primary languages get placeholders:
   ```json
   { "common": { "welcomeBack": "[ES] Welcome back" } }
   ```

7. **Validate locale completeness**
   ```
   en.json:  142/142 keys (100%)
   es.json:  128/142 keys (90.1%) — 14 missing
   fr.json:  135/142 keys (95.1%) — 7 missing
   ```

8. **Find unused translation keys**
   Report keys in locale files never referenced in source code.

## Rules

- Never modify non-primary locale files without user confirmation
- Preserve existing key structure and ordering
- Mark placeholder translations with `[TODO]` or `[LANG]` prefix
- Do not extract developer-facing strings (logs, error codes)
- Handle pluralization using the framework's syntax
- Handle interpolation: `"Hello, {{name}}"` not string concatenation
- Process one file at a time, show changes before applying
- Sort keys alphabetically within namespaces
