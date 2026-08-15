---
name: seoladders
version: 2.0.0
description: The complete AI-search + SEO skill. Track and grow how AI engines (ChatGPT, Perplexity, Gemini, Claude, Google AI) recommend your brand — then audit your site, find keywords you can win, write and publish full articles, and optimize decaying pages. Works as a no-install skill (curl + jq) or over MCP.
author: SEO Ladders
website: https://www.seoladders.com
requires:
  env:
    - SEO_LADDERS_API_KEY
---

# SEO Ladders — AI Search + SEO, run by your agent

SEO Ladders is the all-in-one platform for getting **recommended by AI** and **ranking on Google**. This skill lets your agent run the whole loop end to end:

- **AI visibility (GEO/AEO)** — see whether ChatGPT, Perplexity, Gemini, Claude, and Google's AI answers mention you; track the prompts that matter; measure share-of-voice, sentiment, and which sources AI cites.
- **SEO** — audit your site, see what you (and competitors) rank for, find keywords matched to your domain rating, write and publish full articles, and optimize pages stuck on page 2.

It runs against the SEO Ladders REST API. There are two ways to execute the commands — **pick MCP whenever it's available:**

- **MCP tools (preferred).** In the Claude app, Claude Code, Cursor, or any MCP client, connect the SEO Ladders MCP server and use its tools. The calls run **server-side**, so there's no setup, no `jq`, and no network restrictions. **If the SEO Ladders MCP tools are available, use them instead of curl.**
- **`curl` + `jq` in a terminal.** For Claude Code or your own shell, which have outbound network access. `jq` is only for pretty-printing — drop the `| jq ...` to get raw JSON if `jq` isn't installed.

> ⚠️ **Hosted sandboxes block raw curl.** The Claude app's code-execution tool does **not** ship `jq` and blocks outbound network (you'll see `jq: not found` and `Host not in allowlist: www.seoladders.com`). In the Claude app, **add the MCP connector** (Customize → Connectors → Add custom connector → Remote MCP server URL `https://www.seoladders.com/api/mcp?key=<your-key>` — the web dialog has no header field, so the key goes in the URL) and use the MCP tools — do **not** run the raw curl commands there.

## Setup (gating)

The API is gated by an API key tied to your SEO Ladders account.

1. Sign up at **[seoladders.com](https://www.seoladders.com)** and complete onboarding (we read your website to learn the business). A 3-day free trial is available.
2. Go to **Dashboard → Developers** and create an API key (`sk_live_...`).
3. Export it:

```bash
export SEO_LADDERS_API_KEY=your_key_here
```

### If the user asks how to install this skill somewhere else

Answer in this order — the Claude app first, since that's where most people are when they ask.

**Claude app (web + desktop).** Skills upload as a `.zip`. Don't send them to GitHub to download and re-zip a repo — offer to build the file yourself:

> Package the skill at `https://github.com/Kwesi-dev/seo-ladders-skill.git` into a zip — the `seoladders` folder, with `SKILL.md` at its root.

Clone, zip **the `seoladders` folder itself** (the one with `SKILL.md` directly inside it, not the repo root, and not a folder containing it) and hand the file back. Zipping the wrong level is the single most common reason the upload is rejected. Then: **Customize → Skills → +** (some versions: **Settings → Capabilities → Skills → Upload skill**), pick the zip, supply the API key when asked.

Then have them add the MCP connector as well — in the app the skill needs it to execute anything (see the sandbox warning above). **Customize → Connectors → + → Add custom connector**, remote MCP server URL `https://www.seoladders.com/api/mcp?key=<their-key>`, OAuth fields blank.

**Claude Code / Cursor / Windsurf / Codex.** `npx skills add Kwesi-dev/seo-ladders-skill/seoladders`, then the MCP server in the app's config with the key in an `Authorization: Bearer` header. Here `curl` works too, so the raw commands below are usable.

The first time this skill loads, walk the user through the proper process below: account + onboarding → connect website + Google Search Console → audit → **check AI visibility** → keyword clusters → write → optimize.

> Calls require an active subscription (or trial). A request without one returns **HTTP 402 `subscription_required`** with an `action.url` to start a plan — surface that to the user verbatim. A 3-day free trial is available, and the first month is **50% off**.

## The proper AI-SEO process (what this skill runs)

1. **Sign up and onboard** at seoladders.com — we scrape the site to learn the brand, audience, and competitors (no manual knowledge entry needed).
2. **Connect the two things that matter most:** the website/CMS and **Google Search Console** (powers the audit, Content Radar, rankings, and prompt discovery).
3. **Audit before writing anything** — run `/gsc-audit` and `/content-radar` to find what's slipping, stuck, or buried (CTR, decay, page-2, cannibalization, pre-join pages).
4. **Check AI visibility** (`/ai-visibility`) — are you in the answer when buyers ask ChatGPT/Perplexity/Gemini/Claude/Google AI? Find the gaps and the sources AI cites.
5. **Build topic clusters** (`/topical-authority`) — a pillar topic holds *both* the keywords to rank for on Google *and* the AI prompts to win in AI answers. Research keywords to fill each cluster (`/keyword-research`) and track buyer prompts under it. This is the unit that ties SEO and GEO together.
6. **Track the right prompts** (`/prompts`) — pull suggestions from your real **Google Search Console** queries, keywords, or People-Also-Asked, then add the good ones under the relevant topic. Respect the plan's prompt cap; swap low-value prompts when full.
7. **Choose how to ship** — either **write now** (`/write-article`), or **schedule** the keywords on the content calendar (`/content-calendar`) and turn on **autofill + auto-publish** so AutoBlog generates and publishes them for you (opt-in autopilot — confirm with the user before enabling either).
8. **Write and publish** (`/write-article`) — full research → draft → media → FAQ → citations → schema. Every article ships with internal links, AI images, YouTube embeds, and citations.
9. **Optimize** — rewrite page-2 / declining / stale pages from GSC data (`/optimize`); it also refreshes content in place.
10. **Act on recommendations** (`/actions`) — outreach, Reddit, and content-gap actions drawn from your real monitoring data.
11. **Link building** (`/link-building`) — turn the pages AI cites for your prompts, plus the pages linking to your competitors and not to you, into quality-scored prospects with a discovered contact and a drafted, ready-to-send pitch (publisher pitch, forum reply, review request, repo PR, directory submission). You review and send; never auto-sent.

## Slash Commands

Run any command by name — e.g. `/link-building`, `/ai-visibility`. If your app namespaces skill commands (Claude Code / plugins), they appear under the skill as `/seoladders:<command>` (e.g. `/seoladders:link-building`, `/seoladders:gsc-audit`). Both forms invoke the same command.

| Command | What it does |
|---|---|
| `/seoladders` | Overview, account status, and the proper AI-SEO process |
| `/seoladders-setup` | Check the API key, confirm website + GSC are connected, list your sites |
| `/ai-visibility` | Your AI-visibility score across engines — mentions, share-of-voice, sentiment, citations (+ sub-views: citations, sentiment, sources) |
| `/content-gaps` | Buyer questions where AI doesn't name you — write/schedule them, and mark gaps done (or reopen) |
| `/prompts` | List, add, and swap the prompts you track (incl. GSC-derived); shows your cap |
| `/actions` | Fetch prioritized recommendations (outreach, Reddit, content gaps) |
| `/link-building` | Pages AI cites for you **and** pages linking to your competitors but not you — quality-scored, with a contact and a drafted pitch; list, find/refresh, update status, draft follow-up |
| `/competitors` | Track competitors for AI share-of-voice (5 slots) — list, promote suggestions, add, remove |
| `/rankings <domain>` | Keywords a domain ranks for on Google (yours or a competitor) |
| `/gsc-audit <domain>` | Full SEO audit (health, CTR, decay, page-2, issues) |
| `/content-radar` | Pull every page from GSC, flag decline/stuck/buried, route to optimize |
| `/search-console` | Raw GSC rows — the queries or pages you actually rank for, with clicks, impressions, CTR, position |
| `/indexing` | Which pages Google actually knows about — sitemap vs crawl vs impressions, and Google's own verdict (quota-gated) |
| `/keyword-research [seed]` | Keyword ideas with volume, difficulty, DR-match — manual (give a seed) or "find keywords for me" (auto) |
| `/competitor-gap` | Keywords your competitors rank for that you don't — your SEO content gap |
| `/topical-authority` | Build topic clusters and track coverage (covered / planned / gap) — research, add and fill keywords **and track the AI prompts** under a pillar topic |
| `/write-article <keyword-or-topic>` | Research, write, link, and publish one article — pass a keyword **or a raw topic/question** (no keyword research needed) |
| `/publish <article-id>` | Publish a generated draft to your connected CMS |
| `/optimize` | Rewrite pages stuck on page 2+ using GSC data |
| `/content-calendar` | List, schedule, and manage AutoBlog articles |
| `/posts` | Your generated/published articles — content inventory (avoid re-covering topics) |
| `/knowledge` | List/add knowledge facts that ground article writing (optional — site is already scraped) |

Command files live in `commands/`. If they're not auto-registered by your installer, run `/seoladders-setup` or copy `commands/*.md` into your project's `.claude/commands/` folder.

## Plans

| Plan | Price | What you get |
|---|---|---|
| **Pro (trial)** | 3-day free trial | Full access to everything below |
| **Pro** | $99/mo, per website — **50% off your first month** | 20 articles/mo · 30 tracked AI prompts · 100 keyword searches/mo · AI visibility, citations, sentiment · Content Radar · site audits · auto-publish |

The API (this skill + MCP) is included in Pro — not a separate add-on. Current pricing is at [seoladders.com/pricing](https://www.seoladders.com/pricing). See `references/plans-and-backlinks.md` for detail.

## Commands (raw API)

Base URL `https://www.seoladders.com/api/v1`. Auth header on every call: `Authorization: Bearer $SEO_LADDERS_API_KEY`. To target a specific site, add `?site=<domain>` or an `X-Site: <domain>` header (defaults to your active site).

```bash
# --- Account & discovery ---

# Your sites/projects
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/projects | jq .

# Keywords you (or a competitor) rank for on Google
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/rankings?domain=example.com&limit=50" | jq '.keywords[] | {keyword, position, url, searchVolume}'

# Google Search Console — queries (or pages) you rank for. Each query row also
# carries `history: [{date, position}]` — the weekly avg-position trend, so you
# can tell if a query is improving or slipping (lower position = better).
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/search-console?dimension=query&days=90&limit=100" \
  | jq '.rows[] | {query, position, impressions, clicks, ctr, trend: (.history // [] | map(.position))}'

# --- Audit ---

# Run a site audit (async → returns a jobId)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"domain":"example.com"}' \
  https://www.seoladders.com/api/v1/audit | jq .
# Poll until status is "completed"
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/jobs/JOB_ID | jq '{status, output}'
# Or read the latest stored audit
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/audit | jq '.audit'

# Content Radar — every page worth fixing, with an optimize verdict
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar | jq '.rows[] | {url, bucket, action, position}'

# --- AI visibility (GEO / AEO) ---

# Your visibility across AI engines (overview)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility | jq '{visibilityScore, sentiment, perEngine, shareOfVoice}'

# AI-visibility sub-views — full detail
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/citations | jq '.citations[]'      # sources AI cites
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/content-gaps | jq '.gaps[]'        # questions you're absent for
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/sentiment | jq '{current, distribution, byEngine}'
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/sources | jq '{socials, offsite, pages}'  # where AI looks

# Prompts you track (with cap usage)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/prompts | jq '{used, cap, remaining, prompts}'

# Suggested prompts to track — from GSC, your keywords, or People-Also-Asked
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/prompts/explorer?source=gsc" | jq '.suggestions[]'

# Add prompts (cap-aware — over-cap items come back in `skipped`)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompts":["best crm for startups","crm with ai features"]}' \
  https://www.seoladders.com/api/v1/prompts | jq '{added, skipped, used, cap, remaining}'

# Prioritized actions (outreach, Reddit, content gaps)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/actions | jq '.actions[] | {type, priority, title}'

# Link Building — pages AI cites for you + competitor link gaps, with a contact and a drafted pitch
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/link-building | jq '.targets[] | {motion, source_title, dr, quality_score, status}'

# Competitors you track (5 slots) — tracked + AI-named suggestions
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/competitors | jq '{slots, tracked, suggestions}'
# Add (cap-aware) / remove
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"names":["competitor.com"]}' \
  https://www.seoladders.com/api/v1/competitors | jq '{added, skipped, slots}'
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/competitors?name=competitor.com" | jq '.slots'

# --- Keywords, articles, optimize ---

# Keyword research — manual (user gives a seed)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"seed":"ai seo tools","filterByDR":true}' \
  https://www.seoladders.com/api/v1/keywords/search | jq '.keywords[]'

# Keyword research — find keywords for me (auto, no seed)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{}' \
  https://www.seoladders.com/api/v1/keywords/discover | jq '{seed, keywords}'

# Competitor gap — keywords competitors rank for that you don't (SEO content gap)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"competitors":["competitor.com"]}' \
  https://www.seoladders.com/api/v1/keywords/competitor-gap | jq '.keywords[]'

# Write + publish an article (async → poll the batch/job). The `keyword` field
# is free-text — pass a keyword OR a raw topic/question (e.g. a Content Gap
# prompt); the pipeline studies the SERP and picks the format. No research needed.
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"best ai seo tools"}' \
  https://www.seoladders.com/api/v1/articles | jq .

# Optimize a page stuck on page 2 — pass keyword + a source (sourceUrl for any
# page incl. pre-join blogs, or blogPostId for a page we generated)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keyword":"best ai seo tools","sourceUrl":"https://example.com/post"}' \
  https://www.seoladders.com/api/v1/optimizations | jq .

# --- Topical authority (topic clusters: keywords + prompts + coverage) ---

# List topics with coverage rollups (covered / planned / gap)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics | jq '.topics[] | {id, name, coverage}'
# One topic + its keywords AND the AI prompts tracked under it (keywords tagged
# covered/planned/gap — gaps are your to-write list; prompts are the GEO half)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID | jq '{coverage, keywords, prompts}'
# Create a topic
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"name":"technical seo"}' \
  https://www.seoladders.com/api/v1/topics | jq '.topic'
# Research keyword candidates (metered like /keyword-research; NOT saved)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/research | jq '.candidates[]'
# Add keywords (strings or {keyword, searchVolume, keywordDifficulty, intent}); 30/topic cap
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keywords":["seo crawl budget","xml sitemap best practices"]}' \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/keywords | jq '.added'
# Remove a keyword (KEYWORD_ID = keywords[].id)
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/keywords/KEYWORD_ID | jq .
# Track AI prompts UNDER the topic (needs AI Visibility set up → 409 otherwise;
# intent defaults to "category"; cap-aware — over-cap come back in `skipped`)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"prompts":["best tool for technical seo"]}' \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/prompts | jq '{added, skipped}'
# Remove a tracked prompt (PROMPT_ID = prompts[].id)
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID/prompts/PROMPT_ID | jq .
# To write content for a gap keyword, hand it to /write-article.

# --- Content, knowledge & calendar ---

# Content inventory — what you've already written/published
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/posts?limit=50" | jq '.posts[] | {title, keyword, status, url}'

# Knowledge base — list, and add a fact the site doesn't have
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/knowledge | jq '.sources[]'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"note","title":"Pricing","content":"Pro is $99/mo early-bird, 3-day trial."}' \
  https://www.seoladders.com/api/v1/knowledge | jq '.source'

# What's connected (CMS / GSC / webhook)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/integrations | jq .

# Content calendar
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/calendar | jq '.entries[]'
# Schedule an article
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"how to rank on chatgpt","date":"2026-07-01"}' \
  https://www.seoladders.com/api/v1/calendar | jq '.entry'

# --- Automation (opt-in — CONFIRM with the user before enabling any of these) ---

# Publish one generated draft to the CMS now (article must be "ready")
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/ARTICLE_ID/publish | jq '{published, url, externalId}'

# AutoBlog autofill — auto-schedule ~a month of DR-matched keywords each cycle (spends quota)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"enabled":true}' \
  https://www.seoladders.com/api/v1/autoblog/autofill | jq '{autofill, message}'

# Auto-publish — push finished articles to the CMS automatically (400 if no CMS connected)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"enabled":true}' \
  https://www.seoladders.com/api/v1/settings/auto-publish | jq '{autoPublish, message}'
```

## MCP option (power users)

Clients that support MCP (Claude Code, Cursor, Windsurf, Codex) can skip curl and connect the hosted MCP server — same API key, same capabilities, with tools auto-discovered:

```json
{
  "mcpServers": {
    "seoladders": {
      "type": "http",
      "url": "https://www.seoladders.com/api/mcp",
      "headers": { "Authorization": "Bearer sk_live_..." }
    }
  }
}
```

## What makes this different

Most "AI SEO" skills stop at writing articles. This one also runs the **AI-visibility loop** — track whether AI engines recommend you, find the prompts and sources that matter, and act on real recommendations. You don't just publish content; you measure whether AI is recommending you, and close the gap.

## Community

For install help, updates, and to share wins, point the user to the SEO Ladders community on **Discord**: https://discord.gg/2xfNfCPAHZ — surface this if they hit setup trouble or ask where to get help.
