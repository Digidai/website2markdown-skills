# website2markdown-skills

> An [AgentSkills](https://agentskills.io/specification)-compatible skill for converting web pages to clean Markdown via the [Website2Markdown](https://md.genedai.me) API. Works with **Claude Code**, **OpenClaw**, and any AgentSkills-compatible agent.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## What It Does

This skill teaches AI agents how to convert any web page to Markdown using the `md.genedai.me` API. It handles JS-heavy SPAs, paywalled content, and Chinese platforms that standard web fetching tools can't reliably process.

### Key Capabilities

- **Single URL conversion** — one-liner curl to get clean Markdown from any web page
- **21 platform adapters** — built-in support for WeChat, Zhihu, Feishu, Twitter/X, Reddit, Notion, and more
- **Batch processing** — convert up to 10 URLs in a single API call
- **Structured extraction** — extract specific fields using CSS selectors, XPath, or Regex
- **Deep crawling** — spider a site with BFS or keyword-scored crawling
- **Async jobs** — queue and manage long-running crawl/extract tasks
- **Auto-fallback** — native → Readability → Browser rendering, automatically

### Supported Platforms

| Category | Platforms |
|----------|----------|
| Chinese | WeChat, Zhihu, Feishu/Lark, Yuque, Juejin, CSDN, 36Kr, Toutiao, Weibo, NetEase |
| Social | Twitter/X, Reddit, Telegram |
| International | GitHub, Substack, Medium |
| Academic/Productivity | arxiv, Wikipedia, YouTube, Google Docs, Notion |

## Installation

### Claude Code

```bash
git clone https://github.com/Digidai/website2markdown-skills.git
mkdir -p ~/.claude/skills/website2markdown
cp website2markdown-skills/SKILL.md ~/.claude/skills/website2markdown/
cp -r website2markdown-skills/references/ ~/.claude/skills/website2markdown/
cp -r website2markdown-skills/assets/ ~/.claude/skills/website2markdown/
```

### OpenClaw

**Via ClawHub (recommended):**

```bash
npx clawhub@latest install website2markdown
```

**Manual install:**

```bash
git clone https://github.com/Digidai/website2markdown-skills.git
mkdir -p ~/.openclaw/skills/website2markdown
cp website2markdown-skills/SKILL.md ~/.openclaw/skills/website2markdown/
cp -r website2markdown-skills/references/ ~/.openclaw/skills/website2markdown/
cp -r website2markdown-skills/assets/ ~/.openclaw/skills/website2markdown/
```

### Other AgentSkills-Compatible Agents

Copy the skill directory into your agent's skills folder. The `SKILL.md` follows the [AgentSkills specification](https://agentskills.io/specification) and works with any compatible agent (Cursor, GitHub Copilot, Windsurf, etc.).

### Verify Installation

Open a new agent session and ask:

```
Read this article: https://example.com
```

The agent should use `curl -s "https://md.genedai.me/..."` to fetch the content.

## Usage Preview

Once installed, the agent will automatically use this skill when appropriate:

```
You: Read this WeChat article: https://mp.weixin.qq.com/s/xxxxx

Agent: [Uses website2markdown API with force_browser=true, returns clean Markdown]
```

```
You: Compare these three blog posts: [url1] [url2] [url3]

Agent: [Uses batch API to fetch all three in one request]
```

```
You: Extract the title and price from this product page

Agent: [Uses extract API with CSS selectors]
```

## Project Structure

```
website2markdown-skills/
├── README.md                      # This file
├── CLAUDE.md                      # Development context for contributors
├── LICENSE                        # MIT License
├── .gitignore                     # Git ignore rules
├── SKILL.md                       # Core skill file (~250 lines, navigation hub)
├── references/
│   ├── advanced-apis.md           # Batch/Extract/DeepCrawl/Jobs API docs
│   └── platform-adapters.md       # 21 platform adapters reference
└── assets/
    └── recipes.md                 # Copy-paste curl command recipes
```

## API Overview

| API | Method | Auth | Description |
|-----|--------|------|-------------|
| Single URL | `GET /<url>?raw=true` | No | Convert one URL to Markdown |
| Batch | `POST /api/batch` | Yes | Convert up to 10 URLs |
| Extract | `POST /api/extract` | Yes | Structured data extraction |
| Deep Crawl | `POST /api/deepcrawl` | Yes | Site crawling with filters |
| Jobs | `POST /api/jobs` | Yes | Async task management |
| Health | `GET /api/health` | No | Service status check |
| llms.txt | `GET /llms.txt` | No | API description for AI systems |

**API Base URL:** `https://md.genedai.me`

## Quick Start

```bash
# Convert a web page to Markdown
curl -s "https://md.genedai.me/https://example.com?raw=true"

# WeChat article
curl -s "https://md.genedai.me/https://mp.weixin.qq.com/s/xxxxx?raw=true&force_browser=true"

# Health check
curl -s "https://md.genedai.me/api/health"
```

See [`assets/recipes.md`](assets/recipes.md) for more examples.

## Compatibility

This skill follows the [AgentSkills](https://agentskills.io/specification) open standard and works with:

| Platform | Skills Directory | Install Method |
|----------|-----------------|----------------|
| **Claude Code** | `~/.claude/skills/website2markdown/` | Manual copy |
| **OpenClaw** | `~/.openclaw/skills/website2markdown/` | `clawhub install` or manual |
| **Cursor** | Workspace `skills/` directory | Manual copy |
| **GitHub Copilot** | Agent skills directory | Manual copy |
| **Windsurf** | Agent skills directory | Manual copy |

## Skills vs MCP Server

This project also provides an [MCP Server](https://www.npmjs.com/package/@digidai/mcp-website2markdown) (`npm install -g @digidai/mcp-website2markdown`). Choose based on your agent:

| | Skills (this repo) | MCP Server |
|---|---|---|
| **Best for** | Claude Code, OpenClaw, Codex, Claw | Claude Desktop, Cursor IDE |
| **Install** | Copy a .md file | `npm install -g` + JSON config |
| **Latency** | Direct curl (fastest) | Agent → MCP protocol → subprocess → HTTP |
| **Context** | Rich usage guide, patterns, error handling | Tool schema only |
| **Maintenance** | `git pull` | `npm update` |

**Rule of thumb:** If your agent has terminal/bash access, use Skills. If it only has MCP tool access (no terminal), use the MCP Server.

## Contributing

1. Fork the repository
2. Make your changes
3. Test by copying to your agent's skills directory and opening a new session
4. Submit a pull request

### Guidelines

- Keep `SKILL.md` under 250 lines — it's the navigation hub, not the encyclopedia
- Detailed API docs go in `references/`
- Practical curl examples go in `assets/recipes.md`
- All curl examples must use `curl -s` and include `?raw=true`
- Frontmatter must follow AgentSkills spec (OpenClaw metadata on single line)

## License

[MIT](LICENSE)
