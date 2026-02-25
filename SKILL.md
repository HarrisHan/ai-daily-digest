---
name: ai-daily-digest
description: "Generate a daily tech news digest from 90+ top tech blogs (curated by Karpathy). Trigger with /digest. Fetches RSS feeds, scores articles by relevance/quality, and produces a structured Chinese/English summary. No external API keys needed — uses the agent's own LLM for scoring and summarization."
---

# Tech Daily Digest

Generate a structured daily digest from 90+ top tech blogs curated from [Hacker News Popularity Contest 2025](https://refactoringenglish.com/tools/hn-popularity/).

## Trigger

User says `/digest` or asks for a tech news digest/daily briefing.

## Parameters (ask user if not specified)

| Param | Options | Default |
|-------|---------|---------|
| Time range | 24h / 48h / 72h | 24h |
| Top N articles | 10 / 15 / 20 | 15 |
| Language | zh / en | zh |

## Workflow

### Step 1: Fetch RSS feeds

Run the bundled fetcher script:

```bash
node <skill_dir>/scripts/fetch-rss.mjs --hours <HOURS> --sources <skill_dir>/references/sources.json
```

This outputs JSON to stdout. Capture it. The script:
- Fetches 92 RSS/Atom feeds concurrently (15 parallel, 15s timeout)
- Parses XML (RSS 2.0 + Atom)
- Filters articles by time window
- Outputs sorted JSON array of `{title, link, summary, date, source, sourceUrl}`

### Step 2: Score and classify

From the fetched articles JSON, score each article on three dimensions (1-10):

1. **Relevance** — How relevant to AI, software engineering, security, or tech industry
2. **Quality** — Depth of insight, originality, technical substance
3. **Timeliness** — Breaking news or emerging trend vs. evergreen content

Classify into categories:
- 🤖 AI / ML
- 🔒 Security
- ⚙️ Engineering
- 🛠 Tools / Open Source
- 💡 Opinion / Essay
- 📝 Other

Select the top N articles by total score.

### Step 3: Generate summaries

For each selected article:
1. If `summary` from RSS is sufficient (>100 chars), use it as basis
2. If not, use `web_fetch` to read the full article
3. Generate a structured summary:
   - Chinese title translation (keep original as link text)
   - 2-3 sentence summary covering: core problem → key insight → conclusion
   - Recommendation reason (1 sentence)
   - Keywords (2-3 tags)

### Step 4: Generate trend highlights

Analyze all selected articles together and identify 2-3 macro trends in the tech world today.

### Step 5: Format output

Output as a Telegram-friendly message (not a file):

```
📰 Tech Daily Digest — {date}
来自 Karpathy 推荐的 92 个顶级技术博客

📝 今日看点
{2-3 sentence macro trend summary}

🏆 今日必读 (Top 3)
1. {Chinese title}
   {source} · {relative time}
   {summary}
   🏷️ {keywords}

2. ...
3. ...

📋 更多精选
4. {Chinese title} — {source} · {one-line summary}
5. ...
...

📊 统计：{N} 源 → {M} 篇 → {K} 篇精选
```

## Notes

- **No external API key needed.** The agent itself handles scoring and summarization.
- RSS sources are from Karpathy's curated list of Hacker News top blogs.
- If `exec` is unavailable, fall back to using `web_fetch` on individual RSS URLs (slower but works).
- The script requires Node.js 18+ (available on all OpenClaw instances).
- Source list can be customized by editing `references/sources.json`.
