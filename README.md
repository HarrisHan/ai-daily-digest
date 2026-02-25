# 📰 Tech Daily Digest — OpenClaw Skill

From [Andrej Karpathy](https://x.com/karpathy)'s curated 92 top tech blogs, automatically fetch, score, and summarize the best articles into a daily digest.

**No external API keys required.** Uses the OpenClaw agent's own LLM for scoring and summarization — works with Claude, Gemini, GPT, or any model.

## Quick Start

### Install as OpenClaw Skill

```bash
clawhub install ai-daily-digest
```

Then in any OpenClaw chat:

```
/digest
```

### Manual Installation

```bash
git clone https://github.com/HarrisHan/ai-daily-digest.git
cp -r ai-daily-digest ~/.openclaw/workspace/skills/
```

## How It Works

```
92 RSS Feeds → Concurrent Fetch → Time Filter → LLM Scoring → LLM Summary → Digest
     │              │                  │              │              │           │
  sources.json   fetch-rss.mjs    by hours arg    agent scores   agent writes  Telegram
                 (15 parallel)                    relevance/      2-3 sentence  message
                                                  quality/        summaries
                                                  timeliness
```

## Configuration

### Time Range

```
/digest 48h     # Last 48 hours
/digest 72h     # Last 72 hours
```

### Custom Sources

Edit `references/sources.json` to add/remove RSS feeds:

```json
[
  {"name": "your-blog.com", "xmlUrl": "https://your-blog.com/feed.xml", "htmlUrl": "https://your-blog.com"}
]
```

### Scheduled Daily Digest

```bash
openclaw cron add \
  --name "ai-daily-digest" \
  --cron "0 9 * * *" \
  --tz "Asia/Shanghai" \
  --message "/digest" \
  --announce --exact
```

## RSS Sources

92 feeds curated from [Hacker News Popularity Contest 2025](https://refactoringenglish.com/tools/hn-popularity/), including:

Simon Willison · Paul Graham · Dan Abramov · Gwern · Krebs on Security · Antirez · John Gruber · Troy Hunt · Mitchell Hashimoto · Steve Blank · Eli Bendersky · Fabien Sanglard · and 80 more...

## Project Structure

```
ai-daily-digest/
├── SKILL.md              # OpenClaw skill definition
├── README.md             # This file
├── scripts/
│   └── fetch-rss.mjs     # Concurrent RSS fetcher (Node.js, zero deps)
└── references/
    └── sources.json       # 92 RSS feed sources
```

## License

MIT

## Credits

- RSS sources from [Hacker News Popularity Contest 2025](https://refactoringenglish.com/tools/hn-popularity/)
- Inspired by [ai-daily-digest](https://github.com/vigorX777/ai-daily-digest) by vigorX777
- Built for [OpenClaw](https://github.com/openclaw/openclaw) 🦞
