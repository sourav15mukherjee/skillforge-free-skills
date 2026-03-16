# ⚡ SkillForge Free Skills — AI Skill Files for Claude Code

**Production-ready SKILL.md files you can install in seconds**

![Free](https://img.shields.io/badge/Price-Free-brightgreen)
![Claude Code](https://img.shields.io/badge/Platform-Claude%20Code-blueviolet)
![SKILL.md](https://img.shields.io/badge/Format-SKILL.md-blue)

---

## What's Inside

Twelve high-quality, battle-tested skill files that supercharge your Claude Code workflows — completely free.

| | Skill | Description | Link |
|---|---|---|---|
| 📝 | **Commit Message Generator** | Generate conventional commit messages from staged git changes | [View](./commit-message-generator/) |
| 🔀 | **PR Description Writer** | Generate comprehensive PR descriptions from branch diffs | [View](./pr-description-writer/) |
| 🧪 | **Test Generator** | Generate unit and integration tests with edge cases and mocking | [View](./test-generator/) |
| 🔍 | **Code Reviewer** | Review code for bugs, security issues, and best practice violations | [View](./code-reviewer/) |
| 📖 | **API Docs Writer** | Generate API documentation from route handlers with examples | [View](./api-docs-writer/) |
| ♻️ | **Refactoring Assistant** | Identify code smells and apply systematic refactoring patterns | [View](./refactoring-assistant/) |
| 🌿 | **Git Branch Manager** | Create, manage, and clean up branches with smart naming | [View](./git-branch-manager/) |
| 🔐 | **Environment Config Manager** | Manage .env files, validate required variables, generate .env.example templates, and detect missing config | [View](./env-config-manager/) |
| 📄 | **README Generator** | Analyze project structure and generate a comprehensive README.md with badges, install instructions, and usage examples | [View](./readme-generator/) |
| 📊 | **Data Schema Validator** | Validate JSON/YAML schemas, generate TypeScript interfaces and Zod schemas from data samples, and detect schema drift | [View](./data-schema-validator/) |
| ♿ | **Accessibility Auditor** | Scan web app code for WCAG 2.1 violations — color contrast, alt text, ARIA labels, keyboard navigation, and semantic HTML | [View](./accessibility-auditor/) |
| 🌍 | **i18n/Localization Helper** | Extract hardcoded strings into translation keys, generate locale files, find untranslated strings, and validate locale completeness | [View](./i18n-helper/) |

---

## Install

Each skill is a standalone `SKILL.md` file. Pick the ones you want and copy them into your skills directory.

### Global Install (available in all projects)

```bash
# Clone this repo
git clone https://github.com/your-org/skillforge-free-skills.git
cd skillforge-free-skills

# Copy all skills globally
mkdir -p ~/.claude/skills
for skill in */SKILL.md; do
  cp "$skill" ~/.claude/skills/"$(dirname "$skill").md"
done
```

### Project-Level Install (available in one project)

```bash
# Copy a single skill into your project
mkdir -p .claude/skills
cp commit-message-generator/SKILL.md .claude/skills/commit-message-generator.md
```

### Manual Install

1. Open any skill folder above
2. Copy the contents of `SKILL.md`
3. Save it to `~/.claude/skills/` (global) or `.claude/skills/` (project)

That's it — Claude Code will automatically detect and use the skill when relevant.

---

## Want Custom Skills?

Need a skill tailored to your team's workflow? **SkillForge Builder** lets you describe what you need in plain English and generates a production-ready SKILL.md in seconds.

👉 [**Try the Builder**](https://skillforge-tawny.vercel.app/builder)

---

## Full Catalog

These 12 free skills are just the beginning. Browse the full catalog of free and premium skills:

👉 [**Browse All Skills**](https://skillforge-tawny.vercel.app/skills)

---

## OpenClaw Format

SkillForge also supports the **OpenClaw** format for skill distribution. OpenClaw packages include metadata, versioning, and multi-file support for more complex skills. Check the [SkillForge site](https://skillforge-tawny.vercel.app) for details.

---

## Contributing

PRs are welcome! If you have a skill that you think others would find useful:

1. Fork this repo
2. Create a new folder with your skill ID (kebab-case)
3. Add a `SKILL.md` and a short `README.md`
4. Open a pull request

Please ensure your skill follows the [SKILL.md format](https://docs.anthropic.com) and has been tested with Claude Code.

---

## License

This project is licensed under the MIT License — see the [LICENSE](./LICENSE) file for details.

---

Built with ⚡ SkillForge — https://skillforge-tawny.vercel.app
