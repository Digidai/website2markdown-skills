# website2markdown-skills

> Convert any web page to clean Markdown from your AI agent. One command install, auto-discovered.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![API](https://img.shields.io/badge/API-md.genedai.me-22d3ee)](https://md.genedai.me)
[![Source](https://img.shields.io/badge/Source-website2markdown-black?logo=github)](https://github.com/Digidai/website2markdown)
[![npm](https://img.shields.io/npm/v/@digidai/mcp-website2markdown?label=MCP%20Server)](https://www.npmjs.com/package/@digidai/mcp-website2markdown)

## Quick Install

**Pick your agent, run one command:**

```bash
# Claude Code
git clone https://github.com/Digidai/website2markdown-skills ~/.claude/skills/website2markdown

# OpenAI Codex CLI
git clone https://github.com/Digidai/website2markdown-skills ~/.codex/skills/website2markdown

# Gemini CLI
git clone https://github.com/Digidai/website2markdown-skills ~/.gemini/skills/website2markdown

# OpenClaw (via ClawHub)
npx clawhub@latest install website2markdown

# Cursor / Windsurf / Other agents
git clone https://github.com/Digidai/website2markdown-skills .claude/skills/website2markdown
```

That's it. Open a new session and the skill is auto-discovered.

### Verify

```
You: Read this article: https://example.com
Agent: [Uses md.genedai.me API → returns clean Markdown]
```

### Update

```bash
cd ~/.claude/skills/website2markdown && git pull
```

## What It Does

Teaches your AI agent to convert **any URL** to clean Markdown — JS-heavy SPAs, paywalled content, anti-bot pages, and 21 Chinese/international platforms.

```
You: Read this WeChat article: https://mp.weixin.qq.com/s/abc123

Agent: [Calls md.genedai.me with force_browser=true]
       # 深度解析：大模型在企业的落地实践
       > 作者：张三 | 2026-03-25
       ## 核心观点
       1. 大模型的应用场景正在从实验室走向生产环境...
```

```
You: Compare these 3 blog posts and summarize the differences

Agent: [Uses batch API to fetch all 3 in one request, then compares]
```

```
You: Crawl the Cloudflare docs and save as Markdown files

Agent: [Uses deep crawl API with BFS, saves each page]
```

### Capabilities

| API | What It Does |
|-----|-------------|
| `GET /<url>?raw=true` | Convert one URL to Markdown |
| `POST /api/batch` | Batch convert up to 10 URLs |
| `POST /api/extract` | Extract structured data (CSS/XPath/Regex) |
| `POST /api/deepcrawl` | Crawl a site with BFS or keyword scoring |
| `POST /api/jobs` | Async task queue for long-running work |

### 21 Platform Adapters

| Chinese Platforms | International |
|-------------------|---------------|
| WeChat, Zhihu, Feishu/Lark, Yuque, Juejin, CSDN, 36Kr, Toutiao, Weibo, NetEase | Twitter/X, Reddit, Notion, GitHub, Substack, Medium |

These platforms have anti-bot protection, login walls, or complex JS rendering. The API handles all of it automatically.

## Agent Compatibility

| Agent | Install Path | Command |
|-------|-------------|---------|
| **Claude Code** | `~/.claude/skills/` | `git clone ... ~/.claude/skills/website2markdown` |
| **Codex CLI** | `~/.codex/skills/` | `git clone ... ~/.codex/skills/website2markdown` |
| **Gemini CLI** | `~/.gemini/skills/` | `git clone ... ~/.gemini/skills/website2markdown` |
| **OpenClaw** | ClawHub | `npx clawhub@latest install website2markdown` |
| **Cursor** | Project `.claude/skills/` | `git clone ... .claude/skills/website2markdown` |
| **Windsurf** | Project `.claude/skills/` | `git clone ... .claude/skills/website2markdown` |

Follows the [AgentSkills](https://agentskills.io/specification) open standard.

## Skills vs MCP Server

**Have a terminal?** Use Skills (this repo) — faster, richer context.
**No terminal (Claude Desktop, IDE only)?** Use the [MCP Server](https://www.npmjs.com/package/@digidai/mcp-website2markdown).

| | Skills | MCP Server |
|---|---|---|
| **Latency** | Direct HTTP (fastest) | MCP protocol overhead |
| **Context** | Full patterns, error handling, 21 adapters | Tool schema only |
| **Install** | `git clone` (one command) | `npm install -g` + JSON config |
| **Update** | `git pull` | `npm update -g` |

## Project Structure

```
website2markdown-skills/
├── SKILL.md                 # Core skill — agent reads this
├── references/
│   ├── advanced-apis.md     # Batch, Extract, DeepCrawl, Jobs docs
│   └── platform-adapters.md # 21 platform adapter details
└── assets/
    └── recipes.md           # Copy-paste curl recipes
```

## Contributing

1. Fork → edit → test by copying to your agent's skills dir → PR
2. Keep `SKILL.md` under 250 lines (it's the hub, not the encyclopedia)
3. API details go in `references/`, curl examples in `assets/recipes.md`

## Links

- **Live API**: [md.genedai.me](https://md.genedai.me)
- **Source code**: [Digidai/website2markdown](https://github.com/Digidai/website2markdown)
- **MCP Server**: [@digidai/mcp-website2markdown](https://www.npmjs.com/package/@digidai/mcp-website2markdown)
- **llms.txt**: [md.genedai.me/llms.txt](https://md.genedai.me/llms.txt)

## License

[MIT](LICENSE)
