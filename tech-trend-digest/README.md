# Tech Trend Digest

A plugin that curates personalized tech news digests from authoritative sources. Stay current with software development without doomscrolling.

## What it does

- Searches the web for recent, high-quality tech articles, blog posts, and news
- Filters for authoritative sources and validates freshness (no stale content)
- Produces a structured markdown digest with summaries and source links
- Remembers your preferences (language + topics) so you only set them once
- Tracks what's been reported to avoid duplicates across runs

## Commands

| Command | Description |
|---------|-------------|
| `/digest` | Generate a new tech trend digest |
| `/digest-preferences` | View or update your language and topic preferences |

## How it works

**First run:** You'll be asked to pick a language (English, Japanese, or Both) and select topics (Frontend, Backend, Mobile, AI/ML, DevOps, PM/Culture, Security, or custom). These are saved and reused automatically.

**Every run:** The skill searches multiple sources, validates article freshness (must have a verifiable publication date within 30 days), cross-references against past digests to avoid repeats, and delivers a markdown report with freshness indicators.

## Data files

The plugin stores two files in your `memory/` directory:

- `memory/tech-trend-digest-preferences.md` — Your language and topic settings
- `memory/tech-trend-digest-history.md` — URLs from past digests (for deduplication)

## Freshness indicators

Each article in the digest shows a freshness badge:

- 🟢 Published within 14 days
- 🟡 Published 15-30 days ago (significant items only)
- 🟠 Over 30 days old (rare, only for landmark announcements)
