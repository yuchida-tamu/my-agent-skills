---
name: tech-trend-digest
description: >
  This skill curates a personalized digest of recent tech news, articles, and blog posts
  based on the user's language preference and topic interests. Use this skill whenever the
  user wants to catch up on tech trends, asks "what's new in tech/frontend/AI/etc.", wants
  a news roundup, or mentions staying current with software development. Also trigger when
  the user says "digest", "tech news", "trend report", "what's happening in [tech topic]",
  or "catch me up on [tech area]". Trigger even for casual phrases like "any interesting
  tech news?" or "what did I miss in frontend this week?".
version: 0.1.0
---

# Tech Trend Digest

Curate high-quality, recent tech content and deliver a clean, scannable digest — no fluff,
no noise, only signal. The user relies on this instead of doomscrolling through dozens of feeds,
so be ruthlessly selective.

## Workflow

### Step 1: Load or Gather Preferences

Preferences are set once and remembered across sessions. Check for saved preferences before
asking anything.

**Preferences file location:** `memory/tech-trend-digest-preferences.md`

**If the file exists:** Read it and use the saved language and topics. Do NOT re-ask. Confirm
briefly what's being used:
> "Using your saved preferences: **Both (EN+JP)**, topics: Frontend, AI/ML, Mobile Dev.
> (Say "change preferences" anytime to update these.)"

Then proceed directly to Step 2.

**If the file does NOT exist (first run):** Ask the user using AskUserQuestion with two
questions at once:

**Question 1 — Language preference:**
- English
- Japanese (日本語)
- Both (English + Japanese)

**Question 2 — Topics of interest (multi-select):**
- Frontend (React, Vue, Svelte, CSS, browser APIs)
- Backend (Node.js, Go, Rust, Python, databases, APIs)
- Mobile Dev (React Native, Flutter, Swift, Kotlin)
- AI / ML (LLMs, agents, ML tools, AI-assisted dev)
- DevOps / Infrastructure (CI/CD, containers, cloud, observability)
- Project Management / Engineering Culture (agile, team practices, leadership)
- Security (appsec, supply chain, vulnerabilities)

Custom topics from free-text input are valid too.

**Save preferences immediately** to `memory/tech-trend-digest-preferences.md`:

```markdown
# Tech Trend Digest Preferences

## Language
[English | Japanese | Both]

## Topics
- [topic 1]
- [topic 2]
- ...

## Last updated
[YYYY-MM-DD]
```

**Preference updates:** If the user says "change preferences", "update topics", "switch
language", or similar — re-ask only the relevant question(s), then overwrite the file. For
incremental changes like "add Security to my topics", just append without re-asking everything.

### Step 2: Check History

Read `memory/tech-trend-digest-history.md` if it exists. Extract previously reported URLs
so you can skip them. If the file doesn't exist, this is the first run — proceed without
deduplication.

### Step 3: Research

Conduct thorough web searches to find recent, high-quality content. Depth and relevance
matter more than speed.

**Search strategy — run multiple queries per topic:**

- **Topic-specific:** e.g., "React Server Components 2026", "Rust web framework news"
- **Source-specific:** e.g., "site:vercel.com blog 2026", "site:zenn.dev React"
- **Trend-focused:** e.g., "most popular frontend tools February 2026"
- **Community buzz:** e.g., "Hacker News top posts [topic] this week"

**For Japanese language preference**, also search:
Zenn (zenn.dev), Qiita (qiita.com), Hatena Blog, Publickey (publickey1.jp), Gihyo.jp,
Japanese tech Twitter/X discussions.

**For Both**, search in both languages and organize results by language in the report.

#### Freshness Validation

This is the most critical quality gate. Every candidate article must pass date verification
before inclusion.

**1. Anchor to today's date.** Run `date` at the start of research. All freshness decisions
are relative to this.

**2. Extract the publication date for every candidate.** Check:
- Search result snippet dates
- URL patterns (e.g., `/2026/02/...`)
- Page metadata if fetched

**3. Apply freshness tiers strictly:**
- 🟢 **Within 14 days** — include freely
- 🟡 **15-30 days ago** — include only if it's a major release, breaking change, or landmark
- 🟠 **Over 30 days ago** — exclude by default. Only include truly unmissable items
  (major language release, critical CVE). Flag with "⚠️ Older but significant"

**4. No date = no inclusion.** Undated content is excluded. Exception: official release
changelogs clearly tied to a recent version number — note the missing date in the summary.

**5. Staleness traps to watch for:**
- Evergreen articles from years ago that rank high in search
- Republished/syndicated old content with new dates
- "Updated" articles where only the date was bumped
- Aggregator sites reposting old content with fresh timestamps

#### URL Validation — Every Link Must Point to the Actual Article

This is just as critical as freshness validation. A digest with broken or misleading links
destroys the user's trust. The following rules exist because search results often surface
a domain's index page or a general news feed rather than the specific article. When that
happens, it's tempting to use that URL anyway and pair it with a summary cobbled together
from the search snippet. Do not do this — it's the single fastest way to produce a
low-quality digest.

**Rule 1: Every URL must be a direct link to a specific article page.**

Reject any URL that is:
- A site's homepage or index page (e.g., `example.com/news`, `example.com/blog`)
- A news feed or aggregator listing page (e.g., `crescendo.ai/news/latest-ai-news-and-updates`)
- A category/tag page (e.g., `example.com/topics/ai`)
- A search results page

How to tell: article-specific URLs almost always have a slug, date path, or unique ID
(e.g., `/blog/26/docker-kanvas-cloud-deployment`, `/articles/0bfd00c7c8d898`,
`/news/6-actively-exploited-zero-days/`). If the URL path ends at a generic segment like
`/news`, `/blog`, `/updates`, or `/latest` — it's an index page, not an article.

**Rule 2: Each URL must be unique across the entire digest.**

If two different items point to the same URL, one (or both) is wrong. Never reuse
a URL for different articles. If you find duplicate URLs during composition, you must
either find the correct distinct URL for each item, or drop the item that lacks a
proper URL.

**Rule 3: Verify the URL actually matches the summary content.**

Before including an item, use WebFetch to load the URL and confirm:
- The page title/heading matches (or closely relates to) the article title in your digest
- The page content supports the claims in your summary
- The page is an actual article, not a landing page that merely mentions the topic

If WebFetch is unavailable or the page can't be loaded, you can still include the item
only if the URL clearly appears article-specific from its structure (has a slug/date/ID)
AND the URL came directly from a search result that showed a matching snippet. But
acknowledge the limitation — mark the source with "(link not independently verified)".

**Rule 4: If you can't find a direct article URL, drop the item.**

Never substitute a site's index page or a related-but-different article's URL. It's
better to have 8 items with perfect links than 15 where some send the user to a
homepage. The user will immediately notice and lose trust in the entire digest.

**Rule 5: Pay special attention to aggregator and news-roundup sites.**

Sites like Crescendo AI, LLM Stats, Boston Institute of Analytics blog, etc. often
appear in search results because they mention many topics on a single page. The URL
you get from search is usually the roundup page, not a dedicated article. These are
NOT acceptable as article URLs. Instead, find the primary source they reference —
the actual announcement, blog post, or research paper — and link to that.

#### Quality Filter

Only include content that passes ALL checks:

1. **Authority** — from well-known publications (The Verge, Ars Technica, TechCrunch, InfoQ,
   Smashing Magazine), official project blogs (React, Rust, Go), recognized engineers/writers,
   or established community platforms (Hacker News front page, dev.to trending, Zenn trending).
2. **Freshness** — passes the date validation above.
3. **URL validity** — passes all URL validation rules above. Direct article link, unique,
   content matches summary.
4. **Substance** — skip thin listicles, SEO-bait, auto-generated summaries. Look for original
   reporting, deep dives, substantive tutorials, release announcements, thoughtful opinion pieces.
5. **Not previously reported** — cross-reference against history file URLs.

**Target: 8-15 items.** Quality over quantity. A short digest of 4-5 genuinely fresh items
beats 15 padded with mediocre content. If a topic has nothing noteworthy, say so.

### Step 4: Build the Digest Report

Create a markdown report with this structure:

```markdown
# Tech Trend Digest
**Date:** [today's date]
**Topics:** [selected topics]
**Language:** [language preference]

---

## [Topic Name]

### [Article/Post Title]
**Source:** [Publication or Author] | **Published:** [YYYY-MM-DD or approximate]
**URL:** [full URL]
**Freshness:** [🟢 | 🟡 | 🟠]

[2-4 sentence summary: what happened, why it matters, key takeaway.]

---

## Quick Takes

[One-liner items with URLs for smaller but notable things — minor releases, interesting
threads, small announcements.]
```

**Summaries:** Direct and informative. "What happened and why should I care" — not a rehash.
For tools/releases, mention the problem it solves. For opinion pieces, the core argument.
For tutorials, what you'll learn and skill level.

**Bilingual digests (Both):** Separate with `## 🌐 English Sources` and
`## 🇯🇵 Japanese Sources (日本語)`. Write summaries in the source language.
Japanese-only mode: write the entire report in Japanese.

### Step 5: Save to Outputs

Save as `tech-digest-[YYYY-MM-DD].md` to the outputs directory. Every item must have a
publication date — no exceptions.

### Step 6: Final Audit

One last pass before delivering. For EVERY item in the report, confirm all of the following:

**Freshness check:**
- Has a publication date (exact or approximate)?
- Date is within the acceptable freshness window?
- Freshness indicator (🟢/🟡/🟠) matches the actual age?

**URL integrity check:**
- Is the URL a direct link to a specific article (not an index/landing/category page)?
- Is the URL unique — not used by any other item in this digest?
- Does the URL's domain and path plausibly match the stated source and article title?
- If you fetched the page, does the content match your summary?

**Source-summary consistency check:**
- Does the stated "Source" field match who actually published the content at that URL?
- Does the summary describe what's actually at that URL, not a different article?

Remove any item that fails ANY of these checks. A 6-item digest where every link works
and every summary matches is infinitely more valuable than a 20-item digest with broken
links and mismatched summaries. The user will click these links — they must work.

### Step 7: Update History

Append to `memory/tech-trend-digest-history.md`:

```markdown
## [Date of this digest]
Topics: [topics searched]
URLs reported:
- [url1]
- [url2]
- ...
```

If the file doesn't exist, create it with a header:

```markdown
# Tech Trend Digest History

Tracks previously reported articles to avoid duplication across digest runs.
```

## Important Notes

- **Never fabricate or approximate URLs.** Every URL must come from an actual web search
  result and must point to the specific article — not the site's homepage, news feed, or a
  different article on the same site. If you only found a domain but not the article URL,
  drop the item entirely.
- **Never reuse a URL for multiple items.** If two items share a URL, at least one is wrong.
- **Summary must match the URL.** If someone clicks the link, the page they land on must
  match what the summary describes. A mismatch between summary and link content is the
  worst possible quality failure — it means the digest is unreliable.
- **Prefer primary sources over aggregators.** If a news aggregator mentions a story, find
  the original source (the official blog post, the research paper, the announcement) and
  link to that instead.
- **When in doubt, drop.** One great article with a working link beats five items with
  questionable URLs. The user's trust depends on every link working correctly.
- **Adapt over time.** As history grows, focus on genuinely new developments rather than
  re-covering well-trodden ground.
