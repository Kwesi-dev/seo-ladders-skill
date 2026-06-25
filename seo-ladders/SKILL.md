---
name: seo-ladders
version: 2.0.0
description: The complete AI-search + SEO skill. Track and grow how AI engines (ChatGPT, Perplexity, Gemini, Claude, Google AI) recommend your brand — then audit your site, find keywords you can win, write and publish full articles, earn backlinks, and optimize decaying pages. Works as a no-install skill (curl + jq) or over MCP.
author: SEO Ladders
website: https://www.seoladders.com
requires:
  env:
    - SEO_LADDERS_API_KEY
---

# SEO Ladders — AI Search + SEO, run by your agent

SEO Ladders is the all-in-one platform for getting **recommended by AI** and **ranking on Google**. This skill lets your agent run the whole loop end to end:

- **AI visibility (GEO/AEO)** — see whether ChatGPT, Perplexity, Gemini, Claude, and Google's AI answers mention you; track the prompts that matter; measure share-of-voice, sentiment, and which sources AI cites.
- **SEO** — audit your site, see what you (and competitors) rank for, find keywords matched to your domain rating, write and publish full articles, earn backlinks, and optimize pages stuck on page 2.

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

The first time this skill loads, walk the user through the proper process below: account + onboarding → connect website + Google Search Console → audit → **check AI visibility** → keyword clusters → write + backlink → optimize.

> Calls require an active subscription (or trial). A request without one returns **HTTP 402 `subscription_required`** with an `action.url` to start a plan — surface that to the user verbatim. A 3-day free trial is available, and the first month is **50% off**.

## The proper AI-SEO process (what this skill runs)

1. **Sign up and onboard** at seoladders.com — we scrape the site to learn the brand, audience, and competitors (no manual knowledge entry needed).
2. **Connect the two things that matter most:** the website/CMS and **Google Search Console** (powers the audit, Content Radar, rankings, and prompt discovery).
3. **Audit before writing anything** — run `/gsc-audit` and `/content-radar` to find what's slipping, stuck, or buried (CTR, decay, page-2, cannibalization, pre-join pages).
4. **Check AI visibility** (`/ai-visibility`) — are you in the answer when buyers ask ChatGPT/Perplexity/Gemini/Claude/Google AI? Find the gaps and the sources AI cites.
5. **Track the right prompts** (`/prompts`) — add the buyer questions worth monitoring, including ones derived from your real **Google Search Console** queries. Respect the plan's prompt cap; swap low-value prompts when full.
6. **Plan topic clusters, then research keywords** (`/keyword-research`) — pick keywords your domain rating can realistically win.
7. **Choose how to ship** — either **write now** (`/write-article`), or **schedule** the keywords on the content calendar (`/content-calendar`) and turn on **autofill + auto-publish** so AutoBlog generates and publishes them for you (opt-in autopilot — confirm with the user before enabling either).
8. **Write and publish** (`/write-article`) — full research → draft → media → FAQ → citations → schema. Every article ships with internal links, AI images, YouTube embeds, citations, and 1–2 backlink-exchange links (when available).
9. **Optimize and refresh** — rewrite page-2 pages from GSC data (`/optimize`) and refresh decaying articles (`/content-refresh`).
10. **Act on recommendations** (`/actions`) — outreach, Reddit, and content-gap actions drawn from your real monitoring data.

## Slash Commands

| Command | What it does |
|---|---|
| `/seo-ladders` | Overview, account status, and the proper AI-SEO process |
| `/seo-ladders-setup` | Check the API key, confirm website + GSC are connected, list your sites |
| `/ai-visibility` | Your AI-visibility score across engines — mentions, share-of-voice, sentiment, citations (+ sub-views: citations, sentiment, sources) |
| `/content-gaps` | Buyer questions where AI doesn't name you — what to write to win AI answers |
| `/prompts` | List, add, and swap the prompts you track (incl. GSC-derived); shows your cap |
| `/actions` | Fetch prioritized recommendations (outreach, Reddit, content gaps) |
| `/competitors` | Track competitors for AI share-of-voice (5 slots) — list, promote suggestions, add, remove |
| `/rankings <domain>` | Keywords a domain ranks for on Google (yours or a competitor) |
| `/gsc-audit <domain>` | Full SEO audit (health, CTR, decay, page-2, issues) |
| `/content-radar` | Pull every page from GSC, flag decline/stuck/buried, route to refresh or optimize |
| `/keyword-research [seed]` | Keyword ideas with volume, difficulty, DR-match — manual (give a seed) or "find keywords for me" (auto) |
| `/competitor-gap` | Keywords your competitors rank for that you don't — your SEO content gap |
| `/write-article <keyword>` | Research, write, link, backlink, and publish one article |
| `/publish <article-id>` | Publish a generated draft to your connected CMS |
| `/optimize` | Rewrite pages stuck on page 2+ using GSC data |
| `/content-refresh` | Find and refresh articles whose rankings are decaying |
| `/backlinks` | Check exchange membership, credits, and link targets |
| `/content-calendar` | List, schedule, and manage AutoBlog articles |
| `/posts` | Your generated/published articles — content inventory (avoid re-covering topics) |
| `/knowledge` | List/add knowledge facts that ground article writing (optional — site is already scraped) |

Command files live in `commands/`. If they're not auto-registered by your installer, run `/seo-ladders-setup` or copy `commands/*.md` into your project's `.claude/commands/` folder.

## Plans

| Plan | Price | What you get |
|---|---|---|
| **Pro (trial)** | 3-day free trial | Full access to everything below |
| **Pro** | $99/mo, per website — **50% off your first month** | 20 articles/mo · 30 tracked AI prompts · 100 keyword searches/mo · AI visibility, citations, sentiment · Content Radar · Backlink Exchange · site audits · auto-publish |

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

# Content Radar — every page worth fixing, with a refresh/optimize verdict
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

# Competitors you track (5 slots) — tracked + AI-named suggestions
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/competitors | jq '{slots, tracked, suggestions}'
# Add (cap-aware) / remove
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"names":["competitor.com"]}' \
  https://www.seoladders.com/api/v1/competitors | jq '{added, skipped, slots}'
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/competitors?name=competitor.com" | jq '.slots'

# --- Keywords, articles, optimize, refresh ---

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

# Write + publish an article (async → poll the batch/job)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"best ai seo tools"}' \
  https://www.seoladders.com/api/v1/articles | jq .

# Optimize a page stuck on page 2 — pass keyword + a source (sourceUrl for any
# page incl. pre-join blogs, or blogPostId for a page we generated)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keyword":"best ai seo tools","sourceUrl":"https://example.com/post"}' \
  https://www.seoladders.com/api/v1/optimizations | jq .

# Find decaying articles to refresh
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/refresh-candidates | jq '.candidates[]'

# --- Backlinks & calendar ---

# Backlink Exchange status, credits, targets
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/backlinks | jq '{membership, balance, targets}'

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

# Join the Backlink Exchange — enroll in the DR-weighted network (all fields optional)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{}' \
  https://www.seoladders.com/api/v1/backlinks/join | jq '{joined, membership}'
```

## MCP option (power users)

Clients that support MCP (Claude Code, Cursor, Windsurf, Codex) can skip curl and connect the hosted MCP server — same API key, same capabilities, with tools auto-discovered:

```json
{
  "mcpServers": {
    "seo-ladders": {
      "type": "http",
      "url": "https://www.seoladders.com/api/mcp",
      "headers": { "Authorization": "Bearer sk_live_..." }
    }
  }
}
```

## What makes this different

Most "AI SEO" skills stop at writing articles and trading backlinks. This one also runs the **AI-visibility loop** — track whether AI engines recommend you, find the prompts and sources that matter, and act on real recommendations. You don't just publish content; you measure whether AI is recommending you, and close the gap.
