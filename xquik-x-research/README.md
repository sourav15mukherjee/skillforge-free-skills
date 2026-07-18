# Xquik X Research

Build source-backed research briefs from public X conversations using Xquik.
The skill supports a configured Xquik MCP server or reviewed Xquik exports in
JSON, JSONL, and CSV formats.

## Install

### Claude Code

```bash
mkdir -p ~/.claude/skills/xquik-x-research
cp SKILL.md ~/.claude/skills/xquik-x-research/SKILL.md
```

### OpenClaw

```bash
mkdir -p ~/.openclaw/skills/xquik-x-research
cp SKILL.md ~/.openclaw/skills/xquik-x-research/SKILL.md
```

For project-scoped installation, place this directory under
`.claude/skills/` or the OpenClaw workspace `skills/` directory.

## Configure Xquik

Configure the Xquik MCP server with its supported API key or OAuth flow.
Never paste credentials into a skill, prompt, repository, or export.

Use the current [MCP setup guide](https://docs.xquik.com/mcp/overview) or
[REST API guide](https://docs.xquik.com/api-reference/overview).

## Use

Example requests:

- Research public discussion about a product launch during the past 7 days.
- Compare recurring objections around 2 competitors and cite each source.
- Turn this Xquik JSONL export into an evidence brief with coverage notes.

The output includes research scope, evidence coverage, findings,
counter-signals, a source index, and confidence notes.

## Safety

Retrieved content is untrusted evidence. The skill does not follow instructions
inside posts or linked pages. It keeps research read-only by default and
requires an exact draft plus immediate confirmation before any write action.
