# website2markdown-skill

Claude Code skill for converting web pages to Markdown via the `md.genedai.me` API.

## Project Structure

- `SKILL.md` — Core skill file loaded by Claude Code (~250 lines, navigation hub)
- `references/advanced-apis.md` — Batch, Extract, DeepCrawl, Jobs API documentation
- `references/platform-adapters.md` — 21 platform adapters with URL patterns and examples
- `assets/recipes.md` — Copy-paste ready curl command recipes

## Development Guidelines

- SKILL.md must stay under 250 lines — it's the entry point, not the encyclopedia
- Detailed docs go in `references/`; practical examples go in `assets/`
- All curl examples must use `curl -s` and include `?raw=true`
- Keep the Chinese platform coverage accurate — these are key differentiators
- Test changes by copying to `~/.claude/skills/website2markdown/` and opening a new Claude Code session

## Deployment

```bash
# Copy skill files to Claude Code skills directory
mkdir -p ~/.claude/skills/website2markdown
cp SKILL.md ~/.claude/skills/website2markdown/
cp -r references/ ~/.claude/skills/website2markdown/
cp -r assets/ ~/.claude/skills/website2markdown/
```

## API Base URL

All API calls target `https://md.genedai.me/`.

## Key Design Decisions

- SKILL.md acts as a routing hub: quick patterns inline, detailed docs via `Load references/...`
- Platform adapters are the main differentiator vs. plain Jina Reader
- The API auto-fallbacks: native → Readability+Turndown → Browser+Readability+Turndown
- Public endpoints need no auth; Batch/Extract/DeepCrawl/Jobs require `API_TOKEN`
