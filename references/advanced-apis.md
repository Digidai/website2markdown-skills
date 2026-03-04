# Advanced APIs Reference

Complete documentation for Website2Markdown's authenticated API endpoints.

## Authentication

All advanced APIs require an `API_TOKEN`:

```
Authorization: Bearer <API_TOKEN>
```

Or via query parameter: `?token=<API_TOKEN>`

### Authentication Matrix

| Route | Token Required? | Notes |
|-------|----------------|-------|
| `/<url>?raw=true` | No (public) | Protected only if `PUBLIC_API_TOKEN` is configured |
| `/api/stream` | No (public) | Protected only if `PUBLIC_API_TOKEN` is configured |
| `/api/batch` | Yes (`API_TOKEN`) | Returns 503 if `API_TOKEN` not configured |
| `/api/extract` | Yes (`API_TOKEN`) | Returns 503 if `API_TOKEN` not configured |
| `/api/jobs/*` | Yes (`API_TOKEN`) | Create/query/stream/run |
| `/api/deepcrawl` | Yes (`API_TOKEN`) | Stream and non-stream |
| `/api/health` | No (always public) | Operational metrics |

---

## Batch API

Convert multiple URLs in a single request (up to 10 URLs).

**Endpoint:** `POST /api/batch`

### Request Format

```bash
curl -s -X POST "https://md.genedai.me/api/batch" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "urls": [
      "https://example.com/page1",
      {
        "url": "https://example.com/page2",
        "format": "text",
        "selector": "article",
        "force_browser": false,
        "no_cache": true
      }
    ]
  }'
```

### URL Item Formats

Each item in the `urls` array can be:

| Format | Example | Description |
|--------|---------|-------------|
| String | `"https://example.com/a"` | Uses default settings (markdown format) |
| Object | `{"url": "...", ...}` | Per-URL configuration |

### Object URL Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `url` | string | (required) | Target URL |
| `format` | string | `"markdown"` | Output: `markdown`, `html`, `text`, `json` |
| `selector` | string | — | CSS selector to extract specific elements |
| `force_browser` | boolean | `false` | Force headless Chrome rendering |
| `no_cache` | boolean | `false` | Bypass KV cache |
| `engine` | string | — | Alternative engine: `"jina"` |

### Response Format

```json
{
  "results": [
    {
      "url": "https://example.com/page1",
      "format": "markdown",
      "content": "# Page Title\n\nContent here...",
      "title": "Page Title",
      "method": "readability+turndown",
      "cached": false
    },
    {
      "url": "https://example.com/page2",
      "format": "text",
      "content": "Plain text content...",
      "title": "Page 2",
      "method": "browser+readability+turndown",
      "cached": true
    }
  ]
}
```

### Use Cases

- Compare content from multiple sources
- Batch-read documentation pages
- Collect articles from different platforms in one call

---

## Structured Extraction API

Extract structured fields from web pages using CSS selectors, XPath, or Regex.

**Endpoint:** `POST /api/extract`

### Strategies

| Strategy | Selector Format | Best For |
|----------|----------------|----------|
| `css` | CSS selectors (`h1`, `.class`, `#id`) | Most common; DOM element extraction |
| `xpath` | XPath expressions (`//h1`, `//div[@class="price"]`) | Complex DOM traversal |
| `regex` | Regular expressions (`Price: \$(\d+)`) | Text pattern matching |

### Single URL Extraction

```bash
curl -s -X POST "https://md.genedai.me/api/extract" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "strategy": "css",
    "url": "https://example.com/product",
    "schema": {
      "fields": [
        {"name": "title", "selector": "h1", "type": "text", "required": true},
        {"name": "price", "selector": ".price", "type": "text"},
        {"name": "description", "selector": ".desc", "type": "html"},
        {"name": "image", "selector": "img.hero", "type": "attribute", "attribute": "src"}
      ]
    },
    "include_markdown": true
  }'
```

### Field Types

| Type | Output | Description |
|------|--------|-------------|
| `text` | Plain text | Inner text content of matched element |
| `html` | HTML string | Inner HTML of matched element |
| `attribute` | Attribute value | Requires `attribute` field (e.g., `"attribute": "src"`) |

### Field Options

| Option | Type | Description |
|--------|------|-------------|
| `name` | string | Field name in output |
| `selector` | string | CSS/XPath/Regex selector |
| `type` | string | `text`, `html`, or `attribute` |
| `attribute` | string | Attribute name (required when type is `attribute`) |
| `required` | boolean | Fail if not found (default: `false`) |

### Batch Extraction

Extract from multiple URLs with the same schema:

```bash
curl -s -X POST "https://md.genedai.me/api/extract" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "strategy": "css",
    "items": [
      {"url": "https://example.com/product/1"},
      {"url": "https://example.com/product/2"},
      {"url": "https://example.com/product/3"}
    ],
    "schema": {
      "fields": [
        {"name": "title", "selector": "h1", "type": "text"},
        {"name": "price", "selector": ".price", "type": "text"}
      ]
    }
  }'
```

**Limit:** Max 10 items per batch extraction request.

### XPath Example

```bash
curl -s -X POST "https://md.genedai.me/api/extract" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "strategy": "xpath",
    "url": "https://example.com/article",
    "schema": {
      "fields": [
        {"name": "title", "selector": "//h1", "type": "text"},
        {"name": "author", "selector": "//span[@class=\"author\"]", "type": "text"},
        {"name": "published", "selector": "//time/@datetime", "type": "text"}
      ]
    }
  }'
```

### Regex Example

```bash
curl -s -X POST "https://md.genedai.me/api/extract" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "strategy": "regex",
    "url": "https://example.com/page",
    "schema": {
      "fields": [
        {"name": "version", "selector": "Version:\\s*(\\d+\\.\\d+\\.\\d+)", "type": "text"},
        {"name": "email", "selector": "[\\w.]+@[\\w.]+\\.\\w+", "type": "text"}
      ]
    }
  }'
```

### Response Format

```json
{
  "url": "https://example.com/product",
  "data": {
    "title": "Product Name",
    "price": "$29.99",
    "description": "<p>A great product...</p>",
    "image": "https://example.com/img/hero.jpg"
  },
  "markdown": "# Product Name\n\n...",
  "method": "readability+turndown"
}
```

---

## Deep Crawl API

Crawl an entire site or section with link following, filtering, and relevance scoring.

**Endpoint:** `POST /api/deepcrawl`

### Basic Request

```bash
curl -s -X POST "https://md.genedai.me/api/deepcrawl" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "seed": "https://example.com/docs",
    "max_depth": 2,
    "max_pages": 20,
    "strategy": "best_first",
    "filters": {
      "allow_domains": ["example.com"],
      "url_patterns": ["https://example.com/docs/*"]
    },
    "scorer": {
      "keywords": ["api", "reference"],
      "weight": 2
    }
  }'
```

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `seed` | string | (required) | Starting URL |
| `max_depth` | integer | varies | Maximum link-follow depth |
| `max_pages` | integer | varies | Maximum pages to crawl |
| `strategy` | string | `"bfs"` | Crawl strategy |
| `filters.allow_domains` | string[] | — | Only crawl these domains |
| `filters.url_patterns` | string[] | — | URL glob patterns to include |
| `scorer.keywords` | string[] | — | Keywords for relevance scoring |
| `scorer.weight` | number | `1` | Keyword match weight multiplier |
| `stream` | boolean | `false` | Enable SSE streaming |
| `checkpoint.crawl_id` | string | — | Resume a previous crawl |

### Crawl Strategies

| Strategy | Behavior | Best For |
|----------|----------|----------|
| `bfs` | Breadth-first search, visits all pages at each depth | Complete coverage of a section |
| `best_first` | Prioritizes pages by keyword relevance score | Finding specific content quickly |

### SSE Streaming Mode

Enable with `"stream": true` to receive real-time crawl progress:

```bash
curl -s -N -X POST "https://md.genedai.me/api/deepcrawl" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "seed": "https://example.com/docs",
    "max_depth": 2,
    "max_pages": 10,
    "strategy": "bfs",
    "stream": true
  }'
```

### SSE Event Types

| Event | Data | Description |
|-------|------|-------------|
| `start` | `{"crawl_id": "...", "seed": "..."}` | Crawl initiated |
| `node` | `{"url": "...", "title": "...", "markdown": "...", "depth": N}` | Page crawled |
| `done` | `{"crawl_id": "...", "pages_crawled": N}` | Crawl complete |
| `fail` | `{"url": "...", "error": "..."}` | Page failed |

### Checkpoint / Resume

To resume a crawl that was interrupted:

```bash
curl -s -X POST "https://md.genedai.me/api/deepcrawl" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "seed": "https://example.com/docs",
    "max_depth": 3,
    "max_pages": 50,
    "checkpoint": {
      "crawl_id": "previous-crawl-id"
    }
  }'
```

---

## Job API

Manage long-running async tasks (crawl/extract jobs) with queuing, status tracking, and streaming.

**Endpoint:** `/api/jobs`

### Workflow

```
Create Job → Check Status → Stream Progress → Execute Tasks
   POST         GET          GET /stream       POST /run
```

### 1. Create a Job

```bash
curl -s -X POST "https://md.genedai.me/api/jobs" \
  -H "Authorization: Bearer <API_TOKEN>" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: my-unique-job-id" \
  -d '{
    "type": "crawl",
    "tasks": [
      "https://example.com/page1",
      "https://example.com/page2"
    ],
    "priority": 10,
    "maxRetries": 2
  }'
```

**Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `type` | string | Job type: `"crawl"`, `"extract"` |
| `tasks` | array | URLs or task objects to process |
| `priority` | integer | Higher = processed first (default: `0`) |
| `maxRetries` | integer | Max retry attempts per task (default: `1`) |

**Headers:**

| Header | Description |
|--------|-------------|
| `Idempotency-Key` | Prevents duplicate job creation |

**Response:**

```json
{
  "job_id": "abc-123",
  "status": "queued",
  "tasks_total": 2,
  "created_at": "2025-01-01T00:00:00Z"
}
```

### 2. Check Job Status

```bash
curl -s -H "Authorization: Bearer <API_TOKEN>" \
  "https://md.genedai.me/api/jobs/<job-id>"
```

**Response:**

```json
{
  "job_id": "abc-123",
  "status": "running",
  "tasks_total": 2,
  "tasks_completed": 1,
  "tasks_failed": 0,
  "results": [
    {"url": "...", "content": "...", "status": "done"}
  ]
}
```

**Job Statuses:** `queued` → `running` → `completed` / `failed`

### 3. Stream Progress (SSE)

```bash
curl -s -N -H "Authorization: Bearer <API_TOKEN>" \
  "https://md.genedai.me/api/jobs/<job-id>/stream"
```

Streams SSE events as tasks complete.

### 4. Execute Queued Tasks

```bash
curl -s -X POST -H "Authorization: Bearer <API_TOKEN>" \
  "https://md.genedai.me/api/jobs/<job-id>/run"
```

Triggers execution of queued tasks in the job.

### Complete Job Workflow Example

```bash
# Step 1: Create
JOB=$(curl -s -X POST "https://md.genedai.me/api/jobs" \
  -H "Authorization: Bearer $API_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: batch-$(date +%s)" \
  -d '{"type": "crawl", "tasks": ["https://example.com/a", "https://example.com/b"]}')
JOB_ID=$(echo $JOB | jq -r '.job_id')

# Step 2: Execute
curl -s -X POST -H "Authorization: Bearer $API_TOKEN" \
  "https://md.genedai.me/api/jobs/$JOB_ID/run"

# Step 3: Poll status
curl -s -H "Authorization: Bearer $API_TOKEN" \
  "https://md.genedai.me/api/jobs/$JOB_ID"
```
