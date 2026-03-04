# Platform Adapters Reference

Website2Markdown includes 21 built-in platform adapters that handle platform-specific quirks like login walls, lazy loading, anti-bot protection, and image hotlink restrictions.

## How Adapters Work

When you pass a URL, the API automatically detects the platform and applies the appropriate adapter. No configuration needed — just use the URL as-is.

---

## Chinese Platforms

### WeChat (微信公众号)

| | |
|---|---|
| **Domain** | `mp.weixin.qq.com` |
| **URL Pattern** | `https://mp.weixin.qq.com/s/ARTICLE_ID` |
| **Special Handling** | MicroMessenger User-Agent, image proxy for hotlink bypass, removes subscription prompts |
| **Recommended** | `force_browser=true` for reliable rendering |

```bash
curl -s "https://md.genedai.me/https://mp.weixin.qq.com/s/xxxxx?raw=true&force_browser=true"
```

### Zhihu (知乎)

| | |
|---|---|
| **Domain** | `zhihu.com`, `zhuanlan.zhihu.com` |
| **URL Pattern** | `https://zhuanlan.zhihu.com/p/ARTICLE_ID` or `https://www.zhihu.com/question/ID/answer/ID` |
| **Special Handling** | Login wall removal, lazy image URL swap (`data-actualsrc` → `src`), removes sidebar |

```bash
# Article
curl -s "https://md.genedai.me/https://zhuanlan.zhihu.com/p/123456?raw=true"

# Q&A answer
curl -s "https://md.genedai.me/https://www.zhihu.com/question/12345/answer/67890?raw=true"
```

### Feishu / Lark (飞书)

| | |
|---|---|
| **Domain** | `.feishu.cn`, `.larksuite.com` |
| **URL Pattern** | `https://xxx.feishu.cn/docx/TOKEN` or `https://xxx.feishu.cn/wiki/TOKEN` |
| **Special Handling** | Virtual scroll handling, R2 image storage, UI noise removal (toolbar, sidebar, comments) |
| **Recommended** | Always use `force_browser=true` |

```bash
curl -s "https://md.genedai.me/https://xxx.feishu.cn/docx/xxxxx?raw=true&force_browser=true"
```

### Yuque (语雀)

| | |
|---|---|
| **Domain** | `yuque.com` |
| **URL Pattern** | `https://www.yuque.com/GROUP/BOOK/SLUG` |
| **Special Handling** | SPA rendering, sidebar/TOC removal, lazy content loading |
| **Recommended** | Always use `force_browser=true` |

```bash
curl -s "https://md.genedai.me/https://www.yuque.com/xxx/xxx/xxx?raw=true&force_browser=true"
```

### Juejin (掘金)

| | |
|---|---|
| **Domain** | `juejin.cn` |
| **URL Pattern** | `https://juejin.cn/post/ARTICLE_ID` |
| **Special Handling** | Login popup removal, code block expansion, removes "read more" truncation |

```bash
curl -s "https://md.genedai.me/https://juejin.cn/post/123456?raw=true"
```

### CSDN

| | |
|---|---|
| **Domain** | `csdn.net`, `blog.csdn.net` |
| **URL Pattern** | `https://blog.csdn.net/USER/article/details/ID` |
| **Special Handling** | Login popup removal, code block expansion, ad removal, removes "read more" fold |

```bash
curl -s "https://md.genedai.me/https://blog.csdn.net/user/article/details/123456?raw=true"
```

### 36Kr

| | |
|---|---|
| **Domain** | `36kr.com` |
| **URL Pattern** | `https://36kr.com/p/ARTICLE_ID` |
| **Special Handling** | Stealth browser rendering, content extraction from JS-rendered page |

```bash
curl -s "https://md.genedai.me/https://36kr.com/p/123456?raw=true&force_browser=true"
```

### Toutiao (今日头条)

| | |
|---|---|
| **Domain** | `toutiao.com` |
| **URL Pattern** | `https://www.toutiao.com/article/ID` |
| **Special Handling** | Stealth browser rendering, content extraction, anti-bot bypass |

```bash
curl -s "https://md.genedai.me/https://www.toutiao.com/article/123456?raw=true&force_browser=true"
```

### Weibo (微博)

| | |
|---|---|
| **Domain** | `weibo.com` |
| **URL Pattern** | `https://weibo.com/USER_ID/POST_ID` |
| **Special Handling** | Stealth rendering, hybrid proxy bypass, login wall handling |

```bash
curl -s "https://md.genedai.me/https://weibo.com/1234567890/xxxxx?raw=true&force_browser=true"
```

### NetEase (网易)

| | |
|---|---|
| **Domain** | `163.com` |
| **URL Pattern** | `https://www.163.com/dy/article/ID.html` |
| **Special Handling** | Content extraction, ad/sidebar removal |

```bash
curl -s "https://md.genedai.me/https://www.163.com/dy/article/xxxxx.html?raw=true"
```

---

## Social Media Platforms

### Twitter / X

| | |
|---|---|
| **Domain** | `twitter.com`, `x.com` |
| **URL Pattern** | `https://x.com/USER/status/TWEET_ID` |
| **Special Handling** | Stealth browser rendering, login wall bypass, thread unrolling |
| **Recommended** | Always use `force_browser=true` |

```bash
curl -s "https://md.genedai.me/https://x.com/user/status/123456?raw=true&force_browser=true"
```

### Reddit

| | |
|---|---|
| **Domain** | `reddit.com` |
| **URL Pattern** | `https://reddit.com/r/SUBREDDIT/comments/ID/SLUG` |
| **Special Handling** | Auto-transforms to `old.reddit.com` for cleaner extraction |

```bash
curl -s "https://md.genedai.me/https://reddit.com/r/programming/comments/xxx/title?raw=true"
```

### Telegram

| | |
|---|---|
| **Domain** | `t.me` |
| **URL Pattern** | `https://t.me/CHANNEL/POST_ID` or `https://t.me/s/CHANNEL` |
| **Special Handling** | Public channel post extraction |

```bash
curl -s "https://md.genedai.me/https://t.me/channel_name/123?raw=true"
```

---

## International Platforms

### GitHub

| | |
|---|---|
| **Domain** | `github.com` |
| **URL Pattern** | `https://github.com/OWNER/REPO`, `.../issues/N`, `.../blob/...` |
| **Special Handling** | Repository READMEs, issue/PR content, file content extraction |

```bash
# Repository README
curl -s "https://md.genedai.me/https://github.com/owner/repo?raw=true"

# Issue
curl -s "https://md.genedai.me/https://github.com/owner/repo/issues/123?raw=true"
```

### Substack

| | |
|---|---|
| **Domain** | `substack.com`, `*.substack.com` |
| **URL Pattern** | `https://newsletter.substack.com/p/POST_SLUG` |
| **Special Handling** | Newsletter article extraction, paywall handling |

```bash
curl -s "https://md.genedai.me/https://newsletter.substack.com/p/article-slug?raw=true"
```

### Medium

| | |
|---|---|
| **Domain** | `medium.com`, `*.medium.com` |
| **URL Pattern** | `https://medium.com/@user/POST_SLUG-ID` |
| **Special Handling** | Blog post extraction, paywall bypass attempts |

```bash
curl -s "https://md.genedai.me/https://medium.com/@user/article-title-abc123?raw=true"
```

---

## Academic & Productivity Platforms

### arxiv

| | |
|---|---|
| **Domain** | `arxiv.org` |
| **URL Pattern** | `https://arxiv.org/abs/PAPER_ID` |
| **Special Handling** | Paper abstract and metadata extraction |

```bash
curl -s "https://md.genedai.me/https://arxiv.org/abs/2301.00001?raw=true"
```

### Wikipedia

| | |
|---|---|
| **Domain** | `wikipedia.org`, `*.wikipedia.org` |
| **URL Pattern** | `https://en.wikipedia.org/wiki/ARTICLE` |
| **Special Handling** | Article content extraction, infobox handling |

```bash
curl -s "https://md.genedai.me/https://en.wikipedia.org/wiki/Artificial_intelligence?raw=true"
```

### YouTube

| | |
|---|---|
| **Domain** | `youtube.com`, `youtu.be` |
| **URL Pattern** | `https://www.youtube.com/watch?v=VIDEO_ID` |
| **Special Handling** | Video metadata extraction, transcript/caption retrieval |

```bash
curl -s "https://md.genedai.me/https://www.youtube.com/watch?v=dQw4w9WgXcQ?raw=true"
```

### Google Docs

| | |
|---|---|
| **Domain** | `docs.google.com` |
| **URL Pattern** | `https://docs.google.com/document/d/DOC_ID/...` |
| **Special Handling** | Public document content extraction |

```bash
curl -s "https://md.genedai.me/https://docs.google.com/document/d/DOC_ID/edit?raw=true"
```

### Notion

| | |
|---|---|
| **Domain** | `notion.site`, `notion.so`, `*.notion.site` |
| **URL Pattern** | `https://site.notion.site/PAGE_SLUG-ID` |
| **Special Handling** | SPA rendering, lazy scroll loading, database view handling |
| **Recommended** | Use `force_browser=true` |

```bash
curl -s "https://md.genedai.me/https://site.notion.site/Page-Title-abc123?raw=true&force_browser=true"
```

---

## Troubleshooting

| Problem | Platform | Solution |
|---------|----------|----------|
| Empty content | WeChat, Feishu, Yuque | Add `force_browser=true` |
| Login wall visible | Zhihu, CSDN, Juejin | Should be auto-removed; try `force_browser=true` |
| Images broken/missing | WeChat | Images are proxied automatically; check if URL is valid |
| Truncated content | CSDN, Juejin | "Read more" is auto-expanded; if still truncated, use `selector` |
| Anti-bot block | Twitter/X, 36Kr, Toutiao | Use `force_browser=true`; API uses stealth mode |
| Rate limited | Any | Wait 2-3 seconds between requests |
| Timeout | Large pages | Use `selector` to narrow scope |
| Wrong content | Reddit | Ensure full URL with subreddit path |
| SPA not rendered | Notion, Yuque, Feishu | Must use `force_browser=true` for SPA platforms |
