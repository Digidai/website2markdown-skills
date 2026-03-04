# website2markdown-skill

> A Claude Code skill for converting web pages to clean Markdown via the [Website2Markdown](https://md.genedai.me) API.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

## What It Does

This skill teaches Claude Code how to convert any web page to Markdown using the `md.genedai.me` API. It handles JS-heavy SPAs, paywalled content, and Chinese platforms that WebFetch can't reliably process.

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

### Option 1: Clone and copy

```bash
git clone https://github.com/dai/website2markdown-skill.git
mkdir -p ~/.claude/skills/website2markdown
cp website2markdown-skill/SKILL.md ~/.claude/skills/website2markdown/
cp -r website2markdown-skill/references/ ~/.claude/skills/website2markdown/
cp -r website2markdown-skill/assets/ ~/.claude/skills/website2markdown/
```

### Option 2: Manual copy

Download the files and place them in `~/.claude/skills/website2markdown/`:

```
~/.claude/skills/website2markdown/
├── SKILL.md
├── references/
│   ├── advanced-apis.md
│   └── platform-adapters.md
└── assets/
    └── recipes.md
```

### Verify installation

Open a new Claude Code session and ask:

```
Read this article: https://example.com
```

Claude should use `curl -s "https://md.genedai.me/..."` instead of WebFetch.

## Usage Preview

Once installed, Claude Code will automatically use this skill when appropriate:

```
You: Read this WeChat article: https://mp.weixin.qq.com/s/xxxxx

Claude: [Uses website2markdown API with force_browser=true, returns clean Markdown]
```

```
You: Compare these three blog posts: [url1] [url2] [url3]

Claude: [Uses batch API to fetch all three in one request]
```

```
You: Extract the title and price from this product page

Claude: [Uses extract API with CSS selectors]
```

## Project Structure

```
website2markdown-skill/
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

## Contributing

1. Fork the repository
2. Make your changes
3. Test by copying to `~/.claude/skills/website2markdown/` and opening a new Claude Code session
4. Submit a pull request

### Guidelines

- Keep `SKILL.md` under 250 lines — it's the navigation hub, not the encyclopedia
- Detailed API docs go in `references/`
- Practical curl examples go in `assets/recipes.md`
- All curl examples must use `curl -s` and include `?raw=true`

## License

[MIT](LICENSE)
