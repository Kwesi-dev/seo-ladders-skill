---
name: seoladders
version: 2.0.0
description: The complete AI-search + SEO skill. Track and grow how AI engines (ChatGPT, Perplexity, Gemini, Claude, Google AI Overview, and Google AI Mode) recommend your brand — then audit your site, find keywords you can win, write and publish full articles, and optimize decaying pages. Works as a no-install skill (curl + jq) or over MCP.
author: SEOLadders
website: https://www.seoladders.com
requires:
  env:
    # Needed ONLY for header-setting clients (Claude Code, Cursor, Windsurf, Codex)
    # and raw curl. In the Claude app the connector signs in through SEOLadders and
    # there is no key — see "Setup (gating)".
    - SEO_LADDERS_API_KEY  # optional in the Claude app
---

# SEOLadders — AI Search + SEO, run by your agent

SEOLadders is the all-in-one platform for getting **recommended by AI** and **ranking on Google**. This skill lets your agent run the whole loop end to end:

- **AI visibility (GEO/AEO)** — see whether all six tracked engines mention you (ChatGPT, Perplexity, Gemini, Claude, Google AI Overview, and Google AI Mode — the last two are separate engines); track the prompts that matter; measure share-of-voice, sentiment, and which sources AI cites.
- **SEO** — audit your site, see what you (and competitors) rank for, find keywords matched to your domain rating, write and publish full articles, and optimize pages stuck on page 2.

It runs against the SEOLadders REST API. There are two ways to execute the commands — **pick MCP whenever it's available:**

- **MCP tools (preferred).** In the Claude app, Claude Code, Cursor, or any MCP client, connect the SEOLadders MCP server and use its tools. The calls run **server-side**, so there's no setup, no `jq`, and no network restrictions. **If the SEOLadders MCP tools are available, use them instead of curl.**
- **`curl` + `jq` in a terminal.** For Claude Code or your own shell, which have outbound network access. `jq` is only for pretty-printing — drop the `| jq ...` to get raw JSON if `jq` isn't installed.

> ⚠️ **Hosted sandboxes block raw curl.** The Claude app's code-execution tool does **not** ship `jq` and blocks outbound network (you'll see `jq: not found` and `Host not in allowlist: www.seoladders.com`). In the Claude app, **add the MCP connector** (Customize → Connectors → Add custom connector → name `SEOLadders`, URL `https://www.seoladders.com/api/mcp` → Continue → leave both detected defaults → Add → Connect → sign in; no API key) and use the MCP tools — do **not** run the raw curl commands there.

## Setup (gating)

Access is tied to your SEOLadders account. **How you authenticate depends on the client:**

- **Claude app** — the connector signs you in through SEOLadders (OAuth). There is **no
  key to create and nothing to export**; skip steps 2 and 3 below.
- **Claude Code / Cursor / Windsurf / Codex, or raw curl** — these set request headers,
  so they need an API key. Follow all three steps.

1. Sign up at **[seoladders.com](https://www.seoladders.com)** and complete onboarding (we read your website to learn the business). A 7-day free trial is available.
2. *(header clients and curl only)* Open the **MCP & Skill** page at `/dashboard/developers` and create an API key (`sk_live_...`).
3. Export it:

```bash
export SEO_LADDERS_API_KEY=your_key_here
```

### If the user asks how to install this skill somewhere else

Answer in this order — the Claude app first, since that's where most people are when they ask.

**Claude app (web + desktop).** Skills upload as a `.zip`. Don't send them to GitHub to download and re-zip a repo — offer to build the file yourself:

> Package the skill at `https://github.com/Kwesi-dev/seo-ladders-skill.git` into a zip — the `seoladders` folder, with `SKILL.md` at its root.

Clone, zip **the `seoladders` folder itself** (the one with `SKILL.md` directly inside it, not the repo root, and not a folder containing it) and hand the file back. Zipping the wrong level is the single most common reason the upload is rejected. Then: **Customize → Skills → +** (some versions: **Settings → Capabilities → Skills → Upload skill**), pick the zip, supply the API key when asked.

Then have them add the MCP connector as well — in the app the skill needs it to execute anything (see the sandbox warning above). Walk them through it exactly:

1. **Customize → Connectors → + → Add custom connector**
2. **Name:** `SEOLadders`
3. **URL:** `https://www.seoladders.com/api/mcp` (no key) → **Continue**
4. **Authentication:** leave **Always required** — Claude marks it *Detected*
5. **OAuth client:** leave **No client ID — register one automatically** — also *Detected*. Leave headers empty.
6. **Add** → **Connect** → sign in and approve

Do NOT tell them to paste a key into the URL, and do NOT tell them to leave the OAuth fields blank: the Claude app signs in through SEOLadders. A key is only needed in clients that set request headers (Claude Code, Cursor, Windsurf) or for the REST API.

**Claude Code / Cursor / Windsurf / Codex.** `npx skills add Kwesi-dev/seo-ladders-skill/seoladders`, then the MCP server in the app's config with the key in an `Authorization: Bearer` header. Here `curl` works too, so the raw commands below are usable.

The first time this skill loads, walk the user through the proper process below: account + onboarding → connect website + Google Search Console → audit → **check AI visibility** → keyword clusters → write → optimize.

> Calls require an active subscription (or trial). A request without one returns **HTTP 402 `subscription_required`** with an `action.url` to start a plan and an `instructions_for_agent` line — read it to the user. A 7-day free trial is available, and the first month is **50% off**.

## The rule: show what the agent already did

SEOLadders is an agent that works on its own from the moment the trial starts. It researches keywords and builds topic clusters, schedules and writes articles, audits the site every week, checks how six AI engines answer the user's buyers, finds link prospects and drafts the outreach. This skill is a window onto that work.

So before you research, audit or write anything, **read what already exists and lead with it**, with the dashboard link:

| The user asks... | Read first (free) | Only then, if they want more |
|---|---|---|
| keywords, what to write | `/overview` | The dashboard home: wins, AI visibility, search clicks, articles, audit, recent activity (free) |
| `/search-console [question]` | Your Google Search Console data, any dimension, date range or filter, plus stored trends (free, cached) |
| `/analytics [question]` | Your Google Analytics data: summary, AI-assistant visits, or any GA4 report (free, cached) |
| `/brand-profile` | What the agent knows about your business: identity, audiences, internal links (free) |
| `/usage` | What's left of this cycle's allowances (free) |
| `/settings` | Current autofill and auto-publish settings (free) |
| `/link-exchange` | Your Link Exchange membership, credits and the links placed for you (free) |
| `/my-keywords` | `/keyword-research`, `/keyword-lookup` (metered) |
| write an article | `/write-article` (shows the plan) | today's article only |
| audit my site | `/gsc-audit` (the weekly audit) | `force` re-crawl (metered) |
| my backlinks | `/my-backlinks` (tracked weekly) | `live` check (metered) |
| topics, clusters | `/topical-authority` | changes happen in the dashboard |
| AI visibility | `/ai-visibility`, `/prompt-answers` | |
| how's it going | `/overview` | |
| rankings, clicks, traffic | `/search-console`, `/analytics`, `/rankings` tracked | any Search Console or GA query; both free |

Most API responses carry `instructions_for_agent`: what to tell the user, with the links. Follow it, and read it out verbatim on a `402` or `429`. Nothing in this skill deletes anything; changes like removing a competitor or a topic are made in the dashboard.

## The proper AI-SEO process (what this skill runs)

1. **Sign up and onboard** at seoladders.com — we scrape the site to learn the brand, audience, and competitors (no manual knowledge entry needed).
2. **Connect the two things that matter most:** the website/CMS and **Google Search Console** (powers the audit, Content Radar, rankings, and prompt discovery).
3. **Audit before writing anything** — run `/gsc-audit` and `/content-radar` to find what's slipping, stuck, or buried (CTR, decay, page-2, cannibalization, pre-join pages).
4. **Check AI visibility** (`/ai-visibility`) — are you in the answer when buyers ask ChatGPT/Perplexity/Gemini/Claude/Google AI? Find the gaps and the sources AI cites.
5. **Topic clusters** (`/topical-authority`, `/my-keywords`) — the agent builds them: a pillar topic holds *both* the keywords to rank for on Google *and* the AI prompts to win in AI answers. Show them; `/keyword-research` only for something they don't cover. This is the unit that ties SEO and GEO together.
6. **Track the right prompts** (`/prompts`) — pull suggestions from your real **Google Search Console** queries, keywords, or People-Also-Asked, then add the good ones under the relevant topic. Respect the plan's prompt cap; swap low-value prompts when full.
7. **Let the agent ship** — the agent schedules keywords on the content calendar (`/content-calendar`) and AutoBlog researches, writes and publishes each one on its date. `/write-article` shows that plan and can start today's article early; it never writes off-calendar or in batches. **Autofill and auto-publish are already on** (auto-publish switches itself on when a CMS is connected), so this is the default behaviour, not something to enable. If the user wants to approve each article first, turn auto-publish *off* — review mode is the switch.
8. **Review what it wrote** (`/posts`) — full research → draft → media → FAQ → citations → schema, done by the agent. Every article ships with internal links, AI images, YouTube embeds, and citations.
9. **Optimize** — rewrite page-2 / declining / stale pages from GSC data (`/optimize`); it also refreshes content in place.
10. **Act on recommendations** (`/actions`) — outreach, Reddit, and content-gap actions drawn from your real monitoring data.
11. **Link building** (`/link-building`) — turn the pages AI cites for your prompts, the pages linking to your competitors and not to you, and the best-of lists already ranking for what your buyers search, into quality-scored prospects with a discovered contact and a drafted, ready-to-send pitch (publisher pitch, forum reply, review request, repo PR, directory submission). You review and send; never auto-sent.

## Slash Commands

Run any command by name — e.g. `/link-building`, `/ai-visibility`. If your app namespaces skill commands (Claude Code / plugins), they appear under the skill as `/seoladders:<command>` (e.g. `/seoladders:link-building`, `/seoladders:gsc-audit`). Both forms invoke the same command.

| Command | What it does |
|---|---|
| `/seoladders` | Overview, account status, and the proper AI-SEO process |
| `/seoladders-setup` | Check the API key, confirm website + GSC are connected, list your sites |
| `/my-keywords` | The keywords the agent already chose, by topic cluster, with what is scheduled (free) |
| `/write-article` | The writing plan (what the agent writes and when), and today's article on request |
| `/ai-visibility` | AI visibility, in two features (and `prompt-answers`: what each engine actually said for each prompt). **Prompt Tracking** (default): your score across engines for your tracked prompts — share-of-voice, sentiment, citations (+ sub-views: citations, sentiment, sources). **AI Mentions** (`view: mentions`): where Google AI and ChatGPT already mention or cite you beyond your tracked prompts |
| `/content-gaps` | Buyer questions where AI doesn't name you — write/schedule them, and mark gaps done (or reopen) |
| `/prompts` | List, add, and swap the prompts you track (incl. GSC-derived); shows your cap |
| `/actions` | Fetch prioritized recommendations (outreach, Reddit, content gaps) |
| `/link-building` | Pages AI cites for you, pages linking to your competitors but not you, **and** the roundups ranking for your buyers — quality-scored, with a contact and a drafted pitch; list, find/refresh by source, update status, draft follow-up |
| `/competitors` | Competitors tracked for AI share-of-voice (10 slots on Pro, 5 on trial) — list, promote suggestions, add |
| `/rankings <domain>` | Keywords a domain ranks for on Google (yours or a competitor) |
| `/gsc-audit` | The audit the agent runs every week: health score and every open issue with what to do |
| `/content-radar` | Pull every page from GSC, flag decline/stuck/buried, route to optimize |
| `/indexing` | Which pages Google actually knows about — sitemap vs crawl vs impressions, and Google's own verdict (quota-gated) |
| `/keyword-research [seed]` | New keyword ideas with volume, difficulty, DR-match (metered; read `/my-keywords` first) |
| `/keyword-lookup <keywords>` | Volume, difficulty, CPC and intent for 1-10 keywords you have in mind (metered) |
| `/serp-check <keyword>` | Google's top 10, your rank in the top 100, or who Google's AI Overview cites (metered) |
| `/my-backlinks` | The links pointing at your site and where each came from (free; live check metered) |
| `/site-check <check>` | Page speed, AI readiness, llms.txt, sitemaps, E-E-A-T on your own site (free) |
| `/competitor-gap` | Keywords your competitors rank for that you don't — your SEO content gap |
| `/topical-authority` | The topic clusters the agent built: keywords (covered / planned / gap) and the AI prompts tracked under each (read-only) |
| `/publish <article-id>` | Publish a generated draft to your connected CMS |
| `/optimize` | Rewrite pages stuck on page 2+ using GSC data |
| `/content-calendar` | List, schedule, and manage AutoBlog articles |
| `/posts` | Your generated/published articles — content inventory (avoid re-covering topics) |
| `/knowledge` | The knowledge base the agent writes from: read it, add a fact, correct a section |

Command files live in `commands/`. If they're not auto-registered by your installer, run `/seoladders-setup` or copy `commands/*.md` into your project's `.claude/commands/` folder.

## Plans

| Plan | Price | What you get |
|---|---|---|
| **Pro (trial)** | 7-day free trial | Full access to everything below |
| **Pro** | $97/mo, per website — **50% off your first month** | 30 articles/mo · 30 tracked AI prompts · 100 keyword searches/mo · AI visibility, citations, sentiment · Content Radar · site audits · auto-publish |

The API (this skill + MCP) is included in Pro — not a separate add-on. Current pricing is at [seoladders.com/pricing](https://www.seoladders.com/pricing). See `references/plans-and-backlinks.md` for detail.

### Limits (read before looping)

- **60 requests a minute** per account, across this API and the MCP server together.
- Some actions are **metered** against the same monthly allowances the dashboard uses, so nothing here costs the user more than the app would: keyword research (shared by `/keyword-research`, `/keyword-lookup`, `/serp-check`, `/rankings` and paid `/prompts` suggestions), rank checks, competitor gap (also the live `/my-backlinks` check), site audits, articles, prospect discovery and knowledge additions. A lookup anyone ran recently is served from cache and costs nothing.
- A `429` is a stop sign, not a retry signal: `rate_limited` → wait a minute; a monthly allowance → it is spent until the next billing period. Never loop a metered command over a list; pick what matters and confirm with the user first.

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

# --- What the agent already did (free) ---

# Keywords it chose, by topic cluster, plus everything scheduled from today
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/keywords/found | jq '{summary, scheduled: .scheduled[:10], dashboard}'

# The writing plan; POST writes today's scheduled article (only today's, one at a time)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/today | jq '{plan, articles, today, upcoming: .upcoming[:7]}'

# Links pointing at your site, tracked weekly (POST for a live, metered check)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/backlinks | jq '{summary, domains: .domains[:10]}'

# --- Lookups (metered, cached) ---

curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keywords":["ai seo tools"]}' \
  https://www.seoladders.com/api/v1/keywords/lookup | jq '.keywords'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"ai seo tools","check":"ai-answers"}' \
  https://www.seoladders.com/api/v1/serp | jq '{overview, aiMode}'

# Quick checks on your own site (free): speed | ai-readiness | llms-txt | sitemaps | sitemap-validate | eeat
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"check":"ai-readiness"}' \
  https://www.seoladders.com/api/v1/site-check | jq .

# --- Audit ---

# Ask for an audit: returns the running or last-7-days one for free; {"force":true} re-crawls (metered)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{}' \
  https://www.seoladders.com/api/v1/audit | jq '{auditId, status, alreadyAudited, alreadyRunning}' 
# Poll until status is "completed"
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/audit?id=AUDIT_ID" | jq '{status, health_score}'
# Or read the latest audit with its full report (free)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/audit | jq '{audit, issues: .report.issues[:10]}'

# Content Radar — every page worth fixing, with an optimize verdict
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar | jq '.rows[] | {url, bucket, action, position}'

# --- AI visibility (GEO / AEO) ---

# Your visibility across AI engines (overview)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility | jq '{visibilityScore, visibilityParts, sentiment, perEngine, shareOfVoice, buyingJourney}'

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

# Competitors you track (10 slots on Pro, 5 on trial) — tracked + AI-named suggestions
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/competitors | jq '{slots, tracked, suggestions}'
# Add (cap-aware)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"names":["competitor.com"]}' \
  https://www.seoladders.com/api/v1/competitors | jq '{added, skipped, slots}'

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

# Optimize a page stuck on page 2 — pass keyword + a source (sourceUrl for any
# page incl. pre-join blogs, or blogPostId for a page we generated)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keyword":"best ai seo tools","sourceUrl":"https://example.com/post"}' \
  https://www.seoladders.com/api/v1/optimizations | jq .

# --- Topical authority (topic clusters: keywords + prompts + coverage + authority) ---

# List topics. `coverage` = what was written for the topic; `authority` = what
# it won (articles we published that now rank + prompts whose AI answers name
# the brand), with a `stage` from planned → published → indexed → ranking →
# winning → cited.
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics | jq '.topics[] | {id, name, coverage, authority}'
# One topic + its keywords AND the AI prompts tracked under it (keywords tagged
# covered/planned/gap — gaps are your to-write list; prompts are the GEO half)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/topics/TOPIC_ID | jq '{coverage, authority, keywords, prompts}'
# To get content for a gap keyword, schedule it with /content-calendar; the agent writes it.

# --- Content, knowledge & calendar ---

# Content inventory — what you've already written/published
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/posts?limit=50" | jq '.posts[] | {title, keyword, status, url}'

# Knowledge base — list, add a fact the site doesn't have, correct a generated section
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/knowledge | jq '.sources[]'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"note","title":"Pricing","content":"Pro is $97/mo early-bird, 7-day trial."}' \
  https://www.seoladders.com/api/v1/knowledge | jq '.source'
curl -s -X PATCH -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"content":"Corrected section text"}' \
  https://www.seoladders.com/api/v1/knowledge/SOURCE_ID | jq .

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

# AutoBlog autofill — ON BY DEFAULT. GET the current value before changing it.
# (auto-schedules ~a month of DR-matched keywords each cycle; spends quota)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"enabled":true}' \
  https://www.seoladders.com/api/v1/autoblog/autofill | jq '{autofill, message}'

# Auto-publish — ON BY DEFAULT once a CMS is connected. Send false for review mode.
# (400 if no CMS connected)
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

For install help, updates, and to share wins, point the user to the SEOLadders community on **Discord**: https://discord.gg/2xfNfCPAHZ — surface this if they hit setup trouble or ask where to get help.
