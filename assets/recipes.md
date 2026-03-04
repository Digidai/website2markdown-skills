# Recipes

Copy-paste ready `curl` commands for common Website2Markdown tasks.

**Base URL:** `https://md.genedai.me`

---

## Basic Recipes

### Read a web page as Markdown

```bash
curl -s "https://md.genedai.me/https://example.com/article?raw=true" | head -c 100000
```

### Get JSON with metadata (title, method, timestamp)

```bash
curl -s "https://md.genedai.me/https://example.com/article?format=json&raw=true"
```

### Extract specific section via CSS selector

```bash
curl -s "https://md.genedai.me/https://example.com?raw=true&selector=article"
curl -s "https://md.genedai.me/https://example.com?raw=true&selector=%23main-content"
curl -s "https://md.genedai.me/https://example.com?raw=true&selector=.post-body"
```

> URL-encode `#` as `%23` in selectors.

### Force browser rendering (JS-heavy sites)

```bash
curl -s "https://md.genedai.me/https://spa-app.com?raw=true&force_browser=true"
```

### Get fresh content (bypass cache)

```bash
curl -s "https://md.genedai.me/https://example.com?raw=true&no_cache=true"
```

### Use Jina engine as alternative

```bash
curl -s "https://md.genedai.me/https://example.com?raw=true&engine=jina"
```

### Get plain text (no Markdown formatting)

```bash
curl -s "https://md.genedai.me/https://example.com?format=text&raw=true"
```

### Get cleaned HTML

```bash
curl -s "https://md.genedai.me/https://example.com?format=html&raw=true"
```

---

## Platform-Specific Recipes

### WeChat article

```bash
curl -s "https://md.genedai.me/https://mp.weixin.qq.com/s/ARTICLE_ID?raw=true&force_browser=true" | head -c 100000
```

### Zhihu article

```bash
curl -s "https://md.genedai.me/https://zhuanlan.zhihu.com/p/123456?raw=true" | head -c 100000
```

### Feishu doc

```bash
curl -s "https://md.genedai.me/https://xxx.feishu.cn/docx/TOKEN?raw=true&force_browser=true" | head -c 100000
```

### Twitter/X post

```bash
curl -s "https://md.genedai.me/https://x.com/user/status/123456?raw=true&force_browser=true"
```

### Reddit thread

```bash
curl -s "https://md.genedai.me/https://reddit.com/r/programming/comments/xxx/title?raw=true" | head -c 100000
```

### GitHub README

```bash
curl -s "https://md.genedai.me/https://github.com/owner/repo?raw=true" | head -c 100000
```

### arxiv paper

```bash
curl -s "https://md.genedai.me/https://arxiv.org/abs/2301.00001?raw=true"
```

### YouTube (metadata + transcript)

```bash
curl -s "https://md.genedai.me/https://www.youtube.com/watch?v=VIDEO_ID?raw=true"
```

---

## Advanced Recipes

### Batch convert multiple URLs

```bash
curl -s -X POST "https://md.genedai.me/api/batch" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      "https://example.com/page1",
      "https://example.com/page2",
      "https://example.com/page3"
    ]
  }'
```

### Batch with per-URL options

```bash
curl -s -X POST "https://md.genedai.me/api/batch" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      "https://example.com/page1",
      {"url": "https://spa-app.com/page", "force_browser": true},
      {"url": "https://example.com/data", "format": "json", "selector": "table"}
    ]
  }'
```

### Extract structured data (CSS)

```bash
curl -s -X POST "https://md.genedai.me/api/extract" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "strategy": "css",
    "url": "https://example.com/product",
    "schema": {
      "fields": [
        {"name": "title", "selector": "h1", "type": "text"},
        {"name": "price", "selector": ".price", "type": "text"},
        {"name": "image", "selector": "img.hero", "type": "attribute", "attribute": "src"}
      ]
    }
  }'
```

### Extract with Regex

```bash
curl -s -X POST "https://md.genedai.me/api/extract" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "strategy": "regex",
    "url": "https://example.com/page",
    "schema": {
      "fields": [
        {"name": "version", "selector": "v(\\d+\\.\\d+\\.\\d+)", "type": "text"},
        {"name": "emails", "selector": "[\\w.]+@[\\w.]+\\.\\w+", "type": "text"}
      ]
    }
  }'
```

### Crawl a documentation site

```bash
curl -s -X POST "https://md.genedai.me/api/deepcrawl" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "seed": "https://docs.example.com",
    "max_depth": 2,
    "max_pages": 20,
    "strategy": "bfs",
    "filters": {
      "allow_domains": ["docs.example.com"],
      "url_patterns": ["https://docs.example.com/*"]
    }
  }'
```

### Crawl with keyword scoring

```bash
curl -s -X POST "https://md.genedai.me/api/deepcrawl" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "seed": "https://example.com/docs",
    "max_depth": 3,
    "max_pages": 30,
    "strategy": "best_first",
    "scorer": {
      "keywords": ["authentication", "api", "oauth"],
      "weight": 2
    }
  }'
```

### Stream crawl progress (SSE)

```bash
curl -s -N -X POST "https://md.genedai.me/api/deepcrawl" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "seed": "https://example.com/docs",
    "max_depth": 2,
    "max_pages": 10,
    "stream": true
  }'
```

### Create and run an async job

```bash
# Create
JOB=$(curl -s -X POST "https://md.genedai.me/api/jobs" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: my-job-$(date +%s)" \
  -d '{"type": "crawl", "tasks": ["https://example.com/a", "https://example.com/b"]}')

JOB_ID=$(echo $JOB | jq -r '.job_id')

# Execute
curl -s -X POST -H "Authorization: Bearer $API_TOKEN" \
  "https://md.genedai.me/api/jobs/$JOB_ID/run"

# Check status
curl -s -H "Authorization: Bearer $API_TOKEN" \
  "https://md.genedai.me/api/jobs/$JOB_ID"
```

---

## Diagnostic Recipes

### Health check

```bash
curl -s "https://md.genedai.me/api/health"
```

### Check conversion method used

```bash
curl -sI "https://md.genedai.me/https://example.com?raw=true" | grep -i x-markdown-method
```

### Check cache status

```bash
curl -sI "https://md.genedai.me/https://example.com?raw=true" | grep -i x-cache-status
```

### Check if browser was used

```bash
curl -sI "https://md.genedai.me/https://example.com?raw=true" | grep -i x-browser-rendered
```

### Check for paywall detection

```bash
curl -sI "https://md.genedai.me/https://example.com?raw=true" | grep -i x-paywall-detected
```

### View all response headers

```bash
curl -sI "https://md.genedai.me/https://example.com?raw=true"
```

### Quick connectivity test

```bash
curl -s "https://md.genedai.me/https://example.com?raw=true" | head -c 500
```
