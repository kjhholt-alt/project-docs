# AUTOPILOT — Automated SEO Content Pipeline for PC Bottleneck Analyzer

## What This Is

A Python CLI tool + cron job that runs weekly on Railway. One command (`python autopilot.py run`) does everything:

1. Researches the best keyword to write about this week
2. Generates a full SEO blog post with affiliate links
3. Commits it to your GitHub repo
4. Vercel auto-deploys it (already does this on push)
5. Pings Google to index the new page
6. Posts a tweet about it
7. Posts to Reddit (draft only — you review + post manually, 2 min)
8. Logs everything to Supabase so you can see what's working

**You touch nothing.** You get a Discord notification from ClawBot saying "new post published, tweet sent, Reddit draft ready" and you either ignore it or spend 2 minutes posting the Reddit draft.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────┐
│                    AUTOPILOT CLI                         │
│                  python autopilot.py run                 │
├──────────┬──────────┬──────────┬──────────┬─────────────┤
│ RESEARCH │ GENERATE │ PUBLISH  │ PROMOTE  │   TRACK     │
│          │          │          │          │             │
│ GSC API  │ Claude   │ GitHub   │ Twitter  │  Supabase   │
│ (keywords│ API      │ API      │ API      │  (logs)     │
│  + gaps) │ (write)  │ (commit) │ (tweet)  │             │
│          │          │          │          │  Discord    │
│ Serper   │ Template │ Vercel   │ Reddit   │  (notify)   │
│ (SERP    │ engine   │ (auto-   │ (draft   │             │
│  data)   │          │  deploy) │  only)   │             │
└──────────┴──────────┴──────────┴──────────┴─────────────┘
         ↑                                       │
         │         Railway Cron (weekly)          │
         └────────────────────────────────────────┘
```

---

## Tech Stack

| Component | Tool | Cost | Why |
|-----------|------|------|-----|
| **Content Generation** | Claude API (Sonnet 4.5) | ~$0.50/post | You already pay for this. Sonnet is cheap and good enough for SEO content. |
| **Keyword Research** | Google Search Console API | Free | Tells you what keywords you're ALREADY getting impressions for but not clicks — instant wins. |
| **SERP Analysis** | Serper.dev API | Free (2,500/mo) | Google SERP data. Shows what competitors rank for, what "People Also Ask" questions exist. |
| **Publishing** | GitHub API | Free | Commits the blog post MDX file directly to your repo. Vercel deploys on push. |
| **Google Indexing** | Google Indexing API | Free | Pings Google to crawl your new page immediately instead of waiting days/weeks. |
| **Twitter/X** | X API (Basic) | $100/mo | Auto-posts a tweet with the blog post link. Worth it for backlinks + traffic. OR free with your existing Twitter MCP. |
| **Reddit** | None (manual) | Free | Generates a draft. You copy-paste and post. 2 minutes. Bots get banned on Reddit. |
| **Analytics** | Supabase | Free | Logs every run, tracks which posts get traffic, which keywords convert. |
| **Notifications** | Discord webhook | Free | ClawBot tells you what happened. |
| **Hosting** | Railway cron | ~$5/mo | Runs the pipeline weekly. You already have Railway. |

**Total new cost: ~$5-10/mo** (Railway cron + Claude API usage). X API is $100/mo if you want auto-tweets, or $0 if you use your existing Twitter MCP through ClawBot.

---

## Directory Structure

```
autopilot/
├── autopilot.py              # Main CLI entry point
├── config.py                 # All configuration, env vars
├── CLAUDE.md                 # Claude Code context file
│
├── research/
│   ├── gsc_client.py         # Google Search Console API client
│   ├── serper_client.py      # Serper.dev SERP analysis
│   └── keyword_picker.py     # Picks best keyword for this week
│
├── generate/
│   ├── writer.py             # Claude API content generation
│   ├── templates.py          # Blog post MDX templates
│   ├── affiliate.py          # Amazon affiliate link injection
│   └── meta.py               # SEO meta tags, OG image generation
│
├── publish/
│   ├── github_client.py      # Commits files to repo via API
│   ├── indexing.py            # Google Indexing API ping
│   └── sitemap.py            # Regenerates sitemap.xml
│
├── promote/
│   ├── twitter.py            # X/Twitter API posting
│   ├── reddit_draft.py       # Generates Reddit post draft
│   └── discord_notify.py     # ClawBot webhook notification
│
├── track/
│   ├── supabase_logger.py    # Logs runs, posts, metrics
│   └── performance.py        # Checks past post performance via GSC
│
├── tests/
│   └── ...                   # Tests for each module
│
└── requirements.txt
```

---

## Module Specifications

### 1. `research/keyword_picker.py`

**Purpose:** Automatically finds the best keyword to target this week.

**Logic:**

```
1. Pull last 28 days of data from Google Search Console API
   - Get all queries where pcbottleneck.buildkit.store appeared in results
   - Filter: impressions > 10, position > 10 (you're showing up but not ranking well)
   - Sort by impressions DESC — these are keywords with demand where you're close

2. Pull Serper.dev data for top 5 candidate keywords
   - Get "People Also Ask" questions (these become H2 headings)
   - Get top 5 ranking URLs (analyze their titles/structure)
   - Check keyword difficulty (if available)

3. Cross-reference with already-published posts in Supabase
   - Don't write about a keyword you already covered
   - If a past post is ranking poorly, consider a "refresh" instead of new post

4. Score each keyword:
   - impressions × (1 / position) × (1 if not covered, 0 if covered)
   - Pick the highest scoring keyword

5. If no GSC data yet (new site), fall back to a seed keyword list:
   SEED_KEYWORDS = [
       "is my gpu bottlenecking my cpu",
       "pc bottleneck test free",
       "best gpu for ryzen 5 7600x",
       "best gpu for i5 13600k",
       "cpu bottleneck calculator 2026",
       "how to tell if cpu is bottlenecking gpu",
       "best upgrades for gaming pc bottleneck",
       "pc bottleneck fix guide",
       "gpu bottleneck vs cpu bottleneck",
       "should i upgrade cpu or gpu first",
       "best budget gpu no bottleneck 2026",
       "pc bottleneck analyzer tool free",
       "how to check pc bottleneck windows",
       "ryzen 7 7800x3d bottleneck",
       "i7 14700k gpu bottleneck"
   ]
```

**Output:** `KeywordResult` object with:
- `keyword: str` — the target keyword
- `search_volume_estimate: int` — based on impressions
- `current_position: float | None` — where you rank now
- `people_also_ask: list[str]` — related questions for H2s
- `competitor_titles: list[str]` — what's currently ranking
- `intent: str` — "informational" | "commercial" | "transactional"

---

### 2. `generate/writer.py`

**Purpose:** Generates a full SEO blog post using Claude API.

**System Prompt:**

```
You are an expert PC hardware writer for pcbottleneck.buildkit.store, a free
tool that analyzes desktop PC hardware for bottlenecks and recommends upgrades.

Write SEO-optimized blog posts that:
- Target the given keyword naturally (use it in title, first paragraph, 2-3 H2s)
- Are 1500-2500 words (Google rewards comprehensive content)
- Include a "People Also Ask" section answering related questions
- Naturally mention the PC Bottleneck Analyzer tool with a CTA link
- Include specific hardware recommendations with {AFFILIATE_LINK} placeholders
- Use a knowledgeable but approachable tone — you're helping someone upgrade their rig
- Include comparison tables where relevant (GPU vs GPU, CPU vs CPU)
- End with a clear CTA: "Run our free PC Bottleneck Analyzer to see exactly
  where your system needs improvement"

Format as MDX with:
- Frontmatter (title, description, date, keywords, slug)
- H2 and H3 headings
- At least one comparison table
- At least 2 {AFFILIATE_LINK:product_name} placeholders for Amazon links
- An FAQ section using the People Also Ask questions

NEVER use filler phrases like "in today's world" or "it's important to note".
Write like a real hardware enthusiast, not a content mill.
```

**User Prompt (generated per keyword):**

```
Write a blog post targeting the keyword: "{keyword}"

Context:
- Current top-ranking articles: {competitor_titles}
- People Also Ask questions: {people_also_ask}
- Search intent: {intent}
- Our tool URL: https://pcbottleneck.buildkit.store
- Current date: {today}

Make this post BETTER than what's currently ranking by being more specific,
more current (2026 hardware), and including our free analyzer tool as a
unique differentiator.
```

**Post-processing:**
1. Replace `{AFFILIATE_LINK:product_name}` with actual Amazon affiliate URLs
   - Use a lookup table in `affiliate.py` mapping product names to ASINs
   - Append `?tag=bottleneck20-20` affiliate tag
2. Generate frontmatter with slug derived from keyword
3. Generate SEO meta description (< 160 chars)

**Output:** Complete MDX file ready to commit.

---

### 3. `generate/affiliate.py`

**Purpose:** Maps hardware product mentions to Amazon affiliate links.

```python
AFFILIATE_TAG = "bottleneck20-20"
AMAZON_BASE = "https://www.amazon.com/dp"

# Maintain a lookup of popular hardware ASINs
# Update this quarterly or when new hardware launches
PRODUCT_CATALOG = {
    # GPUs
    "RTX 4060": "B0C7LXBZ2L",
    "RTX 4070": "B0BY5DLNMR",
    "RTX 4070 Super": "B0CS5LJ8FN",
    "RTX 4070 Ti Super": "B0CS5FRCN4",
    "RTX 4080 Super": "B0CS5H5FYT",
    "RTX 4090": "B0BGP8FGNZ",
    "RTX 5070": "UPDATE_ASIN",  # Update when available
    "RTX 5080": "UPDATE_ASIN",
    "RX 7600": "B0CG3BRWHG",
    "RX 7700 XT": "B0CHDB4YMY",
    "RX 7800 XT": "B0CHDBYZWB",
    "RX 7900 XTX": "B0BRBZM1TV",
    "RX 9070 XT": "UPDATE_ASIN",

    # CPUs
    "Ryzen 5 7600X": "B0BBJM2F3C",
    "Ryzen 7 7800X3D": "B0BTZB7F88",
    "Ryzen 9 7950X": "B0BBHHT8LY",
    "Core i5-14600K": "B0CGJ4MLC6",
    "Core i7-14700K": "B0CGJ1WMJ5",
    "Core i9-14900K": "B0CGJDKLB8",
    "Core Ultra 200S": "UPDATE_ASIN",

    # RAM
    "DDR5 32GB Kit": "B0BJ8NMR3M",
    "DDR5 64GB Kit": "B0BMQCQJB7",

    # SSDs
    "Samsung 990 Pro 2TB": "B0BHJJ9Y77",
    "WD Black SN850X 2TB": "B0B7CMZ3QP",
}

def get_affiliate_link(product_name: str) -> str:
    """Returns Amazon affiliate link for a product, or generic search if not found."""
    asin = PRODUCT_CATALOG.get(product_name)
    if asin and asin != "UPDATE_ASIN":
        return f"{AMAZON_BASE}/{asin}?tag={AFFILIATE_TAG}"
    # Fallback: Amazon search URL with affiliate tag
    search_query = product_name.replace(" ", "+")
    return f"https://www.amazon.com/s?k={search_query}&tag={AFFILIATE_TAG}"

def inject_affiliate_links(content: str) -> str:
    """Replace {AFFILIATE_LINK:Product Name} placeholders with real URLs."""
    import re
    pattern = r'\{AFFILIATE_LINK:([^}]+)\}'
    def replacer(match):
        product = match.group(1).strip()
        url = get_affiliate_link(product)
        return f'[{product}]({url})'
    return re.sub(pattern, replacer, content)
```

---

### 4. `publish/github_client.py`

**Purpose:** Commits the generated blog post to your repo.

```python
import httpx
import base64
from datetime import datetime

class GitHubPublisher:
    def __init__(self, token: str, repo: str = "kjhholt-alt/pc-bottleneck-analyzer"):
        self.token = token
        self.repo = repo
        self.base = f"https://api.github.com/repos/{repo}"
        self.headers = {
            "Authorization": f"token {token}",
            "Accept": "application/vnd.github.v3+json"
        }

    async def publish_post(self, slug: str, content: str) -> dict:
        """Commit a blog post MDX file to the repo."""
        # Path in your Next.js app where blog posts live
        # Adjust to match your actual blog directory
        path = f"app/blog/{slug}/page.mdx"

        encoded = base64.b64encode(content.encode()).decode()

        # Check if file exists (for updates vs creates)
        existing_sha = await self._get_file_sha(path)

        payload = {
            "message": f"autopilot: publish '{slug}'",
            "content": encoded,
            "branch": "main",
        }
        if existing_sha:
            payload["sha"] = existing_sha

        async with httpx.AsyncClient() as client:
            resp = await client.put(
                f"{self.base}/contents/{path}",
                headers=self.headers,
                json=payload
            )
            resp.raise_for_status()
            return resp.json()

    async def _get_file_sha(self, path: str) -> str | None:
        """Get SHA of existing file, or None if it doesn't exist."""
        async with httpx.AsyncClient() as client:
            resp = await client.get(
                f"{self.base}/contents/{path}",
                headers=self.headers
            )
            if resp.status_code == 200:
                return resp.json()["sha"]
            return None
```

**Note:** Once this commits to `main`, Vercel auto-deploys. Zero additional work.

---

### 5. `publish/indexing.py`

**Purpose:** Tells Google to index the new page immediately.

```python
from google.oauth2 import service_account
from google.auth.transport.requests import AuthorizedSession

SCOPES = ["https://www.googleapis.com/auth/indexing"]

class GoogleIndexer:
    def __init__(self, credentials_path: str):
        creds = service_account.Credentials.from_service_account_file(
            credentials_path, scopes=SCOPES
        )
        self.session = AuthorizedSession(creds)

    def request_indexing(self, url: str) -> dict:
        """Request Google to crawl and index a specific URL."""
        endpoint = "https://indexing.googleapis.com/v3/urlNotifications:publish"
        body = {
            "url": url,
            "type": "URL_UPDATED"
        }
        resp = self.session.post(endpoint, json=body)
        return resp.json()
```

**Setup required (one-time, 15 min):**
1. Go to Google Cloud Console → create project
2. Enable "Indexing API" and "Search Console API"
3. Create service account → download JSON key
4. In Google Search Console → Settings → Users → add service account email as Owner
5. Store JSON key as Railway env var `GOOGLE_SERVICE_ACCOUNT_JSON`

---

### 6. `promote/twitter.py`

**Purpose:** Auto-posts a tweet about the new blog post.

**Option A: Use your existing Twitter MCP through ClawBot**
```python
# Send command to ClawBot via Discord webhook to tweet
async def tweet_via_clawbot(webhook_url: str, post_title: str, post_url: str, keyword: str):
    """Tell ClawBot to tweet about the new post."""
    message = {
        "content": f"tweet: Just published a new guide: {post_title}. "
                   f"If you're wondering about {keyword}, check it out 👉 {post_url} "
                   f"#pcgaming #pcbuild #hardware"
    }
    async with httpx.AsyncClient() as client:
        await client.post(webhook_url, json=message)
```

**Option B: Direct X API ($100/mo Basic plan)**
```python
import tweepy

class TwitterPromoter:
    def __init__(self, api_key, api_secret, access_token, access_secret):
        auth = tweepy.OAuthHandler(api_key, api_secret)
        auth.set_access_token(access_token, access_secret)
        self.api = tweepy.API(auth)

    def post_tweet(self, post_title: str, post_url: str, keyword: str):
        """Post a tweet promoting the new blog post."""
        tweet = (
            f"🖥️ New guide: {post_title}\n\n"
            f"Free tool to check your PC for bottlenecks 👇\n"
            f"{post_url}\n\n"
            f"#pcgaming #pcbuild #hardware"
        )
        self.api.update_status(tweet[:280])
```

**Recommendation:** Start with Option A (free, uses existing infra). Switch to Option B if you want more control or a dedicated brand account.

---

### 7. `promote/reddit_draft.py`

**Purpose:** Generates a Reddit post draft. Does NOT auto-post (bots get banned).

```python
async def generate_reddit_draft(
    claude_client,
    keyword: str,
    post_url: str,
    post_title: str
) -> dict:
    """Generate a Reddit-ready post that's genuinely helpful, not spammy."""

    prompt = f"""Write a Reddit post for r/buildapc that would be genuinely
helpful to someone searching for "{keyword}".

Rules:
- DO NOT lead with a link or promotion
- Start by sharing useful information or asking an engaging question
- Be conversational and authentic — Reddit users hate marketing speak
- Mention the tool (pcbottleneck.buildkit.store) ONCE, naturally, near the end
- Keep it under 200 words
- Include a good title that would get upvotes on r/buildapc

The blog post URL is: {post_url}
The blog post title is: {post_title}

Output as JSON:
{{
    "subreddit": "buildapc",
    "title": "...",
    "body": "..."
}}

Also generate alternate versions for these subreddits if relevant:
- r/pcgaming
- r/hardware
- r/buildapcforme
"""

    # Call Claude API
    response = await claude_client.messages.create(
        model="claude-sonnet-4-5-20250929",
        max_tokens=1000,
        messages=[{"role": "user", "content": prompt}]
    )
    return response  # Parse JSON from response
```

**Output:** Saved to `drafts/reddit_{date}_{keyword}.json` and sent to Discord for you to review + post.

---

### 8. `promote/discord_notify.py`

**Purpose:** Sends a summary to your ClawBot Discord channel.

```python
async def notify_pipeline_complete(
    webhook_url: str,
    keyword: str,
    post_url: str,
    post_title: str,
    tweet_sent: bool,
    reddit_draft_path: str,
    gsc_indexed: bool
):
    embed = {
        "embeds": [{
            "title": "🚀 Autopilot: New Post Published",
            "color": 0x22d1ee,  # Your cyan accent color
            "fields": [
                {"name": "Keyword", "value": keyword, "inline": True},
                {"name": "Title", "value": post_title, "inline": False},
                {"name": "URL", "value": post_url, "inline": False},
                {"name": "Tweet", "value": "✅ Sent" if tweet_sent else "❌ Failed", "inline": True},
                {"name": "Google Index", "value": "✅ Pinged" if gsc_indexed else "❌ Failed", "inline": True},
                {"name": "Reddit Draft", "value": f"📝 Ready at {reddit_draft_path}", "inline": False},
            ],
            "footer": {"text": "Autopilot SEO Pipeline"}
        }]
    }
    async with httpx.AsyncClient() as client:
        await client.post(webhook_url, json=embed)
```

---

### 9. `track/supabase_logger.py`

**Purpose:** Logs everything so you can see what's working.

**Supabase table: `autopilot_posts`**

```sql
CREATE TABLE autopilot_posts (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    created_at TIMESTAMPTZ DEFAULT now(),
    keyword TEXT NOT NULL,
    slug TEXT NOT NULL UNIQUE,
    title TEXT NOT NULL,
    post_url TEXT NOT NULL,
    word_count INT,
    affiliate_links_count INT,
    tweet_sent BOOLEAN DEFAULT false,
    reddit_posted BOOLEAN DEFAULT false,
    google_indexed BOOLEAN DEFAULT false,
    -- Performance metrics (updated weekly by performance checker)
    impressions_7d INT DEFAULT 0,
    clicks_7d INT DEFAULT 0,
    avg_position FLOAT DEFAULT 0,
    affiliate_clicks INT DEFAULT 0
);
```

**Supabase table: `autopilot_runs`**

```sql
CREATE TABLE autopilot_runs (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    created_at TIMESTAMPTZ DEFAULT now(),
    status TEXT NOT NULL, -- 'success' | 'partial' | 'failed'
    keyword_chosen TEXT,
    post_slug TEXT,
    research_ms INT,
    generation_ms INT,
    publish_ms INT,
    promote_ms INT,
    total_ms INT,
    error_message TEXT,
    claude_tokens_used INT,
    claude_cost_usd FLOAT
);
```

---

### 10. `autopilot.py` — Main Entry Point

```python
#!/usr/bin/env python3
"""
AUTOPILOT — One-click SEO content pipeline for pcbottleneck.buildkit.store

Usage:
    python autopilot.py run           # Full pipeline: research → generate → publish → promote
    python autopilot.py research      # Only pick a keyword
    python autopilot.py generate      # Generate post for a keyword (interactive)
    python autopilot.py publish       # Publish an existing draft
    python autopilot.py performance   # Check how past posts are performing
    python autopilot.py seed          # Generate posts for all seed keywords (bulk)
"""

import asyncio
import time
import click
from config import Config
from research.keyword_picker import KeywordPicker
from generate.writer import PostWriter
from generate.affiliate import inject_affiliate_links
from publish.github_client import GitHubPublisher
from publish.indexing import GoogleIndexer
from promote.twitter import tweet_via_clawbot
from promote.reddit_draft import generate_reddit_draft
from promote.discord_notify import notify_pipeline_complete
from track.supabase_logger import log_run, log_post


@click.group()
def cli():
    pass


@cli.command()
@click.option('--keyword', default=None, help='Override auto keyword selection')
@click.option('--dry-run', is_flag=True, help='Generate but dont publish')
async def run(keyword, dry_run):
    """Full pipeline: research → generate → publish → promote → track"""
    config = Config()
    start = time.time()

    # ===== STEP 1: RESEARCH =====
    click.echo("🔍 Step 1/5: Researching best keyword...")
    picker = KeywordPicker(config)
    if keyword:
        result = await picker.analyze_keyword(keyword)
    else:
        result = await picker.pick_best_keyword()
    click.echo(f"   ✅ Keyword: '{result.keyword}' (est. {result.search_volume_estimate} monthly searches)")

    # ===== STEP 2: GENERATE =====
    click.echo("✍️  Step 2/5: Generating blog post...")
    writer = PostWriter(config)
    raw_post = await writer.generate(result)
    post = inject_affiliate_links(raw_post.content)
    click.echo(f"   ✅ Generated: {raw_post.title} ({raw_post.word_count} words, {raw_post.affiliate_count} affiliate links)")

    if dry_run:
        # Save locally and exit
        with open(f"drafts/{raw_post.slug}.mdx", "w") as f:
            f.write(post)
        click.echo(f"   📝 Draft saved to drafts/{raw_post.slug}.mdx")
        return

    # ===== STEP 3: PUBLISH =====
    click.echo("🚀 Step 3/5: Publishing to GitHub...")
    publisher = GitHubPublisher(config.github_token)
    await publisher.publish_post(raw_post.slug, post)
    post_url = f"https://pcbottleneck.buildkit.store/blog/{raw_post.slug}"
    click.echo(f"   ✅ Published: {post_url}")

    # Ping Google
    click.echo("   📡 Pinging Google Indexing API...")
    try:
        indexer = GoogleIndexer(config.google_credentials_path)
        indexer.request_indexing(post_url)
        indexed = True
        click.echo("   ✅ Google notified")
    except Exception as e:
        indexed = False
        click.echo(f"   ⚠️  Google indexing failed: {e}")

    # ===== STEP 4: PROMOTE =====
    click.echo("📣 Step 4/5: Promoting...")

    # Tweet
    tweet_ok = False
    try:
        await tweet_via_clawbot(
            config.discord_clawbot_webhook,
            raw_post.title, post_url, result.keyword
        )
        tweet_ok = True
        click.echo("   ✅ Tweet sent via ClawBot")
    except Exception as e:
        click.echo(f"   ⚠️  Tweet failed: {e}")

    # Reddit draft
    reddit_draft = await generate_reddit_draft(
        writer.client, result.keyword, post_url, raw_post.title
    )
    draft_path = f"drafts/reddit_{raw_post.slug}.json"
    with open(draft_path, "w") as f:
        import json
        json.dump(reddit_draft, f, indent=2)
    click.echo(f"   📝 Reddit draft saved: {draft_path}")

    # ===== STEP 5: TRACK =====
    click.echo("📊 Step 5/5: Logging run...")
    elapsed = int((time.time() - start) * 1000)
    await log_post(config, raw_post, post_url)
    await log_run(config, "success", result.keyword, raw_post.slug, elapsed, raw_post.tokens_used)

    # Discord notification
    await notify_pipeline_complete(
        config.discord_webhook,
        result.keyword, post_url, raw_post.title,
        tweet_ok, draft_path, indexed
    )
    click.echo(f"\n✅ Done in {elapsed/1000:.1f}s. Post live at {post_url}")


@cli.command()
async def performance():
    """Check how past posts are performing in Google Search Console."""
    config = Config()
    # Pull GSC data for all published posts
    # Update impressions_7d, clicks_7d, avg_position in Supabase
    # Print a summary table
    click.echo("📊 Checking post performance...")
    # Implementation pulls from GSC API and updates supabase_logger
    pass


@cli.command()
async def seed():
    """Bulk-generate posts for all seed keywords. Use for initial content push."""
    config = Config()
    from research.keyword_picker import SEED_KEYWORDS
    click.echo(f"🌱 Generating {len(SEED_KEYWORDS)} seed posts...")
    for i, kw in enumerate(SEED_KEYWORDS):
        click.echo(f"\n--- [{i+1}/{len(SEED_KEYWORDS)}] {kw} ---")
        # Run the full pipeline for each keyword
        # Add a delay between posts to avoid rate limits
        await asyncio.sleep(5)


if __name__ == "__main__":
    cli(_anyio_backend="asyncio")
```

---

## Configuration

### Environment Variables (Railway)

```bash
# Claude API
ANTHROPIC_API_KEY=sk-ant-...

# GitHub
GITHUB_TOKEN=ghp_...
GITHUB_REPO=kjhholt-alt/pc-bottleneck-analyzer

# Google (base64 encoded JSON key)
GOOGLE_SERVICE_ACCOUNT_JSON=eyJ...

# Serper.dev
SERPER_API_KEY=...

# Supabase
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_SERVICE_KEY=eyJ...

# Discord
DISCORD_CLAWBOT_WEBHOOK=https://discord.com/api/webhooks/...
DISCORD_NOTIFY_WEBHOOK=https://discord.com/api/webhooks/...

# Twitter (if using direct API instead of ClawBot)
# TWITTER_API_KEY=...
# TWITTER_API_SECRET=...
# TWITTER_ACCESS_TOKEN=...
# TWITTER_ACCESS_SECRET=...

# Site config
SITE_URL=https://pcbottleneck.buildkit.store
AFFILIATE_TAG=bottleneck20-20
```

### Railway Cron Schedule

```
# Run every Monday at 9:00 AM UTC
0 9 * * 1    cd /app && python autopilot.py run
```

Optional: run `performance` check on Fridays to update metrics:
```
0 9 * * 5    cd /app && python autopilot.py performance
```

---

## One-Time Setup Checklist

These are the manual steps that can't be automated. Do them once, then never again.

### 1. Google Cloud Setup (15 min)
- [ ] Go to console.cloud.google.com
- [ ] Create new project "autopilot-seo"
- [ ] Enable "Indexing API"
- [ ] Enable "Search Console API"
- [ ] Create service account → download JSON key
- [ ] In Google Search Console → verify pcbottleneck.buildkit.store
- [ ] Add service account email as Owner in Search Console

### 2. Serper.dev (2 min)
- [ ] Sign up at serper.dev (free 2,500 searches/month)
- [ ] Copy API key

### 3. GitHub Personal Access Token (2 min)
- [ ] GitHub → Settings → Developer Settings → Personal Access Tokens → Fine-grained
- [ ] Scope: repo contents (read/write) for pc-bottleneck-analyzer repo
- [ ] Copy token

### 4. Supabase Tables (5 min)
- [ ] Run the two CREATE TABLE statements from section 9 above
- [ ] Or have Claude Code run them via MCP

### 5. Discord Webhook (2 min)
- [ ] Create webhook in your preferred Discord channel
- [ ] Copy URL

### 6. Blog Template in Next.js App (Claude Code does this)
- [ ] Create `/app/blog/[slug]/page.tsx` dynamic route
- [ ] Create MDX rendering component with affiliate link styling
- [ ] Add blog index page at `/app/blog/page.tsx`
- [ ] Update sitemap generation to include blog posts
- [ ] Add `<head>` SEO meta component for blog posts

### 7. Railway Deployment (5 min)
- [ ] Create new Railway service from autopilot directory
- [ ] Add all env vars
- [ ] Set up cron trigger

**Total one-time setup: ~30-45 minutes of manual clicks.**

---

## What You Touch Weekly: NOTHING

Once set up, the system runs every Monday:
1. **Monday 9am UTC:** Autopilot runs. Picks keyword, writes post, publishes, tweets, generates Reddit draft.
2. **Monday ~9:05am UTC:** You get a Discord notification with the summary.
3. **Optional (2 min):** Open the Reddit draft, copy-paste to r/buildapc. Or don't.
4. **Friday 9am UTC:** Performance check updates metrics in Supabase.

That's it. You go back to building MoneyPrinter or whatever else you want. The SEO content machine runs in the background, building your site's authority and affiliate revenue week by week.

---

## Expected Timeline

| Week | Posts | Est. Monthly Impressions | Est. Monthly Clicks |
|------|-------|-------------------------|-------------------|
| 1-2 | Seed 5 posts (manual bulk run) | 0-100 | 0-5 |
| 3-6 | +4 autopilot posts | 500-2,000 | 20-80 |
| 7-12 | +4 more posts (13 total) | 2,000-10,000 | 100-500 |
| 13-24 | +12 more posts (25 total) | 10,000-50,000 | 500-2,500 |

At 2,500 monthly clicks with a 5% affiliate conversion rate = 125 Amazon clicks/month.
At an average $15 commission per hardware sale = **$1,875/month potential** at scale.

This takes 6+ months to build up. SEO is a snowball. But you set it up once and it compounds forever while you sleep.

---

## Commands for Claude Code

Tell Claude Code:

> "Read AUTOPILOT_SEO_PIPELINE.md and build the autopilot system for PC Bottleneck Analyzer. Start with the directory structure and config, then implement each module one at a time. Deploy to Railway when done."

Claude Code has all the MCP servers needed (GitHub, Supabase, Railway) to build and deploy this entire system.

---

*This spec is designed to be handed directly to Claude Code for implementation. Every module has clear inputs, outputs, and logic. No ambiguity.*
