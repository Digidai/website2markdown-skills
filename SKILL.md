---
name: website2markdown
description: Convert web pages to clean Markdown using the Website2Markdown API (md.genedai.me). Use when you need to fetch, read, extract, or analyze web page content — especially when WebFetch fails, returns incomplete content, or the target is JS-heavy/paywalled. Supports 21 platform adapters (WeChat, Zhihu, Feishu, Twitter/X, etc.), batch processing, structured extraction, and deep crawling.
version: "1.0.0"
license: MIT
allowed-tools: Bash, Read, Write, WebFetch
user-invocable: true
metadata: {"openclaw": {"requires": {"bins": ["curl"]}, "primaryEnv": "API_TOKEN", "emoji": "📄", "homepage": "https://github.com/Digidai/website2markdown-skills"}}
---

# Website2Markdown

Convert any web page to clean Markdown via the `md.genedai.me` API.

## When to Use This Skill

**ALWAYS prefer this skill over WebFetch when:**
- WebFetch fails, returns blocked/incomplete content, or redirects
- Target is a JS-heavy SPA (React, Vue, Angular apps)
- Content is behind paywalls or anti-bot protection
- Target is a Chinese platform (WeChat, Zhihu, Feishu, Yuque, Juejin, CSDN, 36Kr, Toutiao, Weibo)
- You need batch conversion of multiple URLs
- You need structured data extraction from pages
- You need to crawl/spider a site

**Also use when:**
- User asks to read/fetch/scrape a web page
- User wants to convert a URL to Markdown
- User needs content from social platforms (Twitter/X, Reddit, Telegram)

## Decision Tree

```
User provides a URL
  │
  ├─ Single page, simple site ──▶ Try WebFetch first
  │     └─ WebFetch fails? ──▶ Use website2markdown
  │
  ├─ Chinese platform / JS-heavy / paywalled ──▶ Use website2markdown directly
  │
  ├─ Multiple URLs needed ──▶ Use website2markdown batch API
  │
  ├─ Need structured fields (title, author, price) ──▶ Use extract API
  │
  └─ Need to crawl entire site/section ──▶ Use deepcrawl API
```

## Quick Reference

### Single URL → Markdown

```bash
curl -s "https://md.genedai.me/https://example.com/page?raw=true"
```

Bare domain also works (auto-prepends `https://`):

```bash
curl -s "https://md.genedai.me/example.com/page?raw=true"
```

Alternative via Accept header:

```bash
curl -s -H "Accept: text/markdown" "https://md.genedai.me/https://example.com/page"
```

**IMPORTANT:** Always use `?raw=true` or Accept header to get plain text. Without it, you get an HTML preview page.

### Output Formats

| Parameter | Output | When to use |
|-----------|--------|-------------|
| `?raw=true` | Plain Markdown | Default for reading content |
| `?format=json&raw=true` | JSON `{url, title, markdown, method, timestamp}` | When you need metadata |
| `?format=html&raw=true` | Cleaned HTML | When you need HTML structure |
| `?format=text&raw=true` | Plain text (no formatting) | When you need raw text only |

### Query Parameters

| Parameter | Purpose | Example |
|-----------|---------|---------|
| `raw=true` | Return plain text instead of HTML preview | Always use this |
| `selector=<css>` | Extract specific elements (max 256 chars) | `selector=article`, `selector=.content` |
| `force_browser=true` | Force headless Chrome rendering | JS-heavy or anti-bot sites |
| `no_cache=true` | Bypass KV cache for fresh content | When content may have changed |
| `engine=jina` | Route through Jina Reader API | Alternative conversion engine |

## Usage Patterns

### Pattern 1: Read a Single Article

```bash
curl -s "https://md.genedai.me/https://example.com/article?raw=true" | head -c 100000
```

Always pipe through `head -c 100000` for potentially large pages to avoid flooding context.

### Pattern 2: Extract Specific Content via CSS Selector

```bash
curl -s "https://md.genedai.me/https://example.com?raw=true&selector=article"
curl -s "https://md.genedai.me/https://example.com?raw=true&selector=%23main-content"
```

URL-encode `#` as `%23` in selectors.

### Pattern 3: JS-Heavy or Anti-Bot Sites

```bash
curl -s "https://md.genedai.me/https://spa-app.com?raw=true&force_browser=true"
```

### Pattern 4: Get Structured JSON (title + content)

```bash
curl -s "https://md.genedai.me/https://example.com?format=json&raw=true"
```

Returns: `{"url": "...", "title": "...", "markdown": "...", "method": "...", "timestamp": "..."}`

### Pattern 5: Chinese & Social Platforms

These work out of the box — the API has built-in adapters:

```bash
# WeChat article
curl -s "https://md.genedai.me/https://mp.weixin.qq.com/s/xxxxx?raw=true&force_browser=true"

# Zhihu article
curl -s "https://md.genedai.me/https://zhuanlan.zhihu.com/p/123456?raw=true"

# Twitter/X
curl -s "https://md.genedai.me/https://x.com/user/status/123456?raw=true&force_browser=true"

# Reddit
curl -s "https://md.genedai.me/https://reddit.com/r/programming/comments/xxx?raw=true"
```

> For all 21 supported platforms with URL patterns and troubleshooting, load `references/platform-adapters.md`.

## Advanced APIs

All advanced APIs require `Authorization: Bearer <API_TOKEN>` header.

| API | Endpoint | Purpose |
|-----|----------|---------|
| **Batch** | `POST /api/batch` | Convert up to 10 URLs in one request |
| **Extract** | `POST /api/extract` | Structured field extraction (CSS/XPath/Regex) |
| **Deep Crawl** | `POST /api/deepcrawl` | Crawl a site with filtering and scoring |
| **Jobs** | `POST /api/jobs` | Async task queue for long-running operations |
| **Health** | `GET /api/health` | Service health check (no auth needed) |
| **llms.txt** | `GET /llms.txt` | API description for AI discoverability |

### Batch API (quick example)

```bash
curl -s -X POST "https://md.genedai.me/api/batch" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["https://example.com/a", "https://example.com/b"]}'
```

### Extract API (quick example)

```bash
curl -s -X POST "https://md.genedai.me/api/extract" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"strategy": "css", "url": "https://example.com", "schema": {"fields": [{"name": "title", "selector": "h1", "type": "text"}]}}'
```

### Deep Crawl API (quick example)

```bash
curl -s -X POST "https://md.genedai.me/api/deepcrawl" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{"seed": "https://docs.example.com", "max_depth": 2, "max_pages": 20, "strategy": "bfs"}'
```

> For complete API documentation with all parameters, response formats, and examples, load `references/advanced-apis.md`.
> For copy-paste ready curl commands, load `assets/recipes.md`.

## Authentication Matrix

| Route | Auth Required? |
|-------|---------------|
| `/<url>?raw=true` | No (public) |
| `/api/batch` | Yes (`API_TOKEN`) |
| `/api/extract` | Yes (`API_TOKEN`) |
| `/api/deepcrawl` | Yes (`API_TOKEN`) |
| `/api/jobs/*` | Yes (`API_TOKEN`) |
| `/api/health` | No |

**Token format:** `Authorization: Bearer <token>` header, or `?token=<token>` query parameter.

## Supported Platforms (21 Adapters)

| Category | Platforms |
|----------|----------|
| **Chinese** | WeChat, Zhihu, Feishu/Lark, Yuque, Juejin, CSDN, 36Kr, Toutiao, Weibo, NetEase |
| **Social** | Twitter/X, Reddit, Telegram |
| **International** | GitHub, Substack, Medium |
| **Academic/Productivity** | arxiv, Wikipedia, YouTube, Google Docs, Notion |

> For URL patterns, special handling details, and troubleshooting per platform, load `references/platform-adapters.md`.

## Response Headers

| Header | Meaning |
|--------|---------|
| `X-Markdown-Method` | `native`, `readability+turndown`, or `browser+readability+turndown` |
| `X-Cache-Status` | `HIT` or `MISS` |
| `X-Browser-Rendered` | `true` if headless Chrome was used |
| `X-Paywall-Detected` | `true` if paywall was triggered |
| `X-Markdown-Fallbacks` | Comma-separated fallback strategies applied |

## Implementation Notes for Agents

1. **Always use `curl -s`** — silent mode suppresses progress output
2. **Always add `?raw=true`** — without it you get an HTML preview page, not markdown
3. **Pipe through `head -c 100000`** for large pages to prevent context overflow
4. **URL-encode special characters** in the target URL if needed (`#` → `%23`, `?` → `%3F` in the path)
5. **No auth needed for single-URL conversion** — just use the GET endpoint
6. **Rate limited per IP** — avoid rapid successive calls; add 1-2s delay between multiple sequential calls
7. **Auto-fallback** — the API tries native → Readability → Browser automatically
8. **For Chinese platforms**, prefer using `force_browser=true` for best results
9. **Jina fallback** — automatically used as last resort when Readability extraction produces little content

## Error Handling

| Symptom | Solution |
|---------|----------|
| Empty content returned | Add `force_browser=true` |
| Timeout error | Use `selector` to narrow scope, or the page is too large |
| 429 Too Many Requests | Wait a few seconds and retry |
| 503 Service Unavailable | For protected endpoints: check API_TOKEN; for rate limit: wait and retry |
| Garbled/incomplete content | Try `engine=jina` as alternative |
| Platform-specific failure | Verify URL format matches adapter patterns |

## Reference Materials

| Document | Content | Lines |
|----------|---------|-------|
| `references/advanced-apis.md` | Batch, Extract, DeepCrawl, Jobs API — full parameters, request/response formats | ~350 |
| `references/platform-adapters.md` | 21 platform adapters — URL patterns, special handling, troubleshooting | ~300 |
| `assets/recipes.md` | Copy-paste curl recipes — basic, platform, advanced, diagnostic | ~180 |
