# website2markdown-skill

AgentSkills-compatible skill for converting web pages to Markdown via the `md.genedai.me` API.
Works with Claude Code, OpenClaw, and any AgentSkills-compatible agent.

## Project Structure

- `SKILL.md` — Core skill file (~250 lines, navigation hub) with AgentSkills frontmatter
- `references/advanced-apis.md` — Batch, Extract, DeepCrawl, Jobs API documentation
- `references/platform-adapters.md` — 21 platform adapters with URL patterns and examples
- `assets/recipes.md` — Copy-paste ready curl command recipes

## Development Guidelines

- SKILL.md must stay under 250 lines — it's the entry point, not the encyclopedia
- Detailed docs go in `references/`; practical examples go in `assets/`
- All curl examples must use `curl -s` and include `?raw=true`
- Keep the Chinese platform coverage accurate — these are key differentiators
- SKILL.md frontmatter must follow AgentSkills spec; OpenClaw `metadata` must be single-line JSON
- Test changes on both Claude Code and OpenClaw if possible

## Deployment

```bash
# Claude Code
mkdir -p ~/.claude/skills/website2markdown
cp SKILL.md ~/.claude/skills/website2markdown/
cp -r references/ ~/.claude/skills/website2markdown/
cp -r assets/ ~/.claude/skills/website2markdown/

# OpenClaw
mkdir -p ~/.openclaw/skills/website2markdown
cp SKILL.md ~/.openclaw/skills/website2markdown/
cp -r references/ ~/.openclaw/skills/website2markdown/
cp -r assets/ ~/.openclaw/skills/website2markdown/
```

## API Base URL

All API calls target `https://md.genedai.me/`.

## Key Design Decisions

- SKILL.md acts as a routing hub: quick patterns inline, detailed docs via `Load references/...`
- Platform adapters are the main differentiator vs. plain Jina Reader
- The API auto-fallbacks: native → Readability+Turndown → Browser+Readability+Turndown
- Public endpoints need no auth; Batch/Extract/DeepCrawl/Jobs require `API_TOKEN`
- Follows AgentSkills open standard for cross-platform compatibility (Claude Code, OpenClaw, Cursor, etc.)
