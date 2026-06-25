# SEO Ladders — AI Search + SEO Skill

AI-search + SEO for any AI agent. SEO Ladders provides the infrastructure: real keyword data matched to your domain rating, a Google Search Console audit, full article writing + CMS publishing, a backlink exchange, a content calendar — and, uniquely, the **AI-visibility loop**: it measures whether ChatGPT, Perplexity, Gemini, Claude, and Google AI actually recommend your brand, finds the prompts and citation sources that matter, and tells you exactly what to fix.

Most "AI SEO" skills stop at writing articles and trading backlinks. This one also measures whether AI is recommending you — and closes the gap.

---

## 1. How it works — the proper AI-SEO process

This is the full loop the skill runs (and walks you through the first time it loads). Steps 1–2 are your one-time setup — that's where you create the API key the **Install** section below needs.

1. **Sign up and onboard** at [seoladders.com](https://seoladders.com) — we scrape the site to learn the brand, audience, and competitors. A **3-day free trial** is available, and the first month is **50% off**.
2. **Connect** the two things that matter most: the website/CMS and Google Search Console. Then create your API key at **Dashboard → Developers** (`sk_live_...`).
3. **Audit before writing anything** (`/gsc-audit` + `/content-radar`) — find what's slipping, stuck, or buried.
4. **Check AI visibility** (`/ai-visibility`) — are you in the answer when buyers ask ChatGPT/Perplexity/Gemini/Claude/Google AI? Find the gaps and the sources AI cites.
5. **Track the right prompts** (`/prompts`) — including ones derived from your real Google Search Console queries. Respect the cap; swap low-value prompts when full.
6. **Plan topic clusters**, then do keyword research to fill them (`/keyword-research`).
7. **Choose how to ship** — either write now (`/write-article`), or schedule the keywords on the content calendar (`/content-calendar`) and turn on **autofill + auto-publish** so AutoBlog generates and publishes them for you (opt-in autopilot — confirm with the user before enabling either).
8. **Write and publish** (`/write-article`). Every article ships with internal links, AI images, YouTube embeds, citations, and 1–2 backlink-exchange links (when available).
9. **Optimize** pages stuck on page 2+ (`/optimize`) and **refresh** decaying articles (`/content-refresh`).
10. **Act on the recommendations** (`/actions`) — outreach, Reddit, and content gaps from your real data.

Full setup walkthrough: `seo-ladders/references/onboarding-guide.md`.

---

## 2. Install — pick how you'll use it

The same `sk_live_...` key works on every surface. Choose your tool:

### Claude Code  (or Cursor / Windsurf / Codex)

Two ways — use either:

**A) Install the skill** — gets the full process + slash commands:

```bash
npx skills add Kwesi-dev/seo-ladders-skill/seo-ladders
```

**B) Connect the MCP server** — tools auto-discovered, no curl:

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

### Claude app  (web + desktop)

Add it as a **Skill**. You upload a small `.zip` of the skill folder — here's how to make it (no command line needed):

**Step 1 — get the skill folder**

On this repo's GitHub page, click the green **Code** button → **Download ZIP**, then unzip it. Inside you'll find a folder called **`seo-ladders`** (it holds `SKILL.md`, `commands/`, and `references/`).

**Step 2 — zip just the `seo-ladders` folder**

- **Mac:** right-click the `seo-ladders` folder → **Compress "seo-ladders"** → you get `seo-ladders.zip`.
- **Windows:** right-click the `seo-ladders` folder → **Send to → Compressed (zipped) folder**.

> Zip the **`seo-ladders` folder itself** (the one with `SKILL.md` inside) — not the whole repo.

**Step 3 — upload it to Claude**

1. **Customize → Skills → +** (add a personal skill).
2. Upload `seo-ladders.zip`.
3. It appears under **Personal skills** with a *slash command + auto* trigger; Claude runs `/ai-visibility`, `/write-article`, `/gsc-audit`, etc.
4. Provide your API key when asked.

**Optional — also add the MCP connector** so your key is stored for execution:

- **Customize → Connectors → Add custom connector** → `https://www.seoladders.com/api/mcp` → header `Authorization: Bearer sk_live_...`

### ChatGPT

Build a **Custom GPT** that calls the API:

1. **Create a GPT → Configure → Actions → Import from URL** → `https://www.seoladders.com/api/openapi.json`
2. **Authentication → API Key → Bearer** → paste your `sk_live_...`
3. Paste `seo-ladders/SKILL.md` into the GPT's **Instructions** (this gives it the process + commands).
4. Ask it to "check my AI visibility", "audit my site", or "write an article for &lt;keyword&gt;" — it runs the real calls.

> **Per-user keys:** a *published* GPT with API-Key auth uses one key for everyone. So each user should create their **own** Custom GPT with their own key — or, on ChatGPT Pro/Business, add the MCP server (`/api/mcp`) as a custom connector (per-user auth).

### Any terminal / other AI

Export the key and use the `curl` commands (see **Commands (raw API)** below) — works anywhere, no install:

```bash
export SEO_LADDERS_API_KEY=sk_live_...
```

Base URL `https://www.seoladders.com/api/v1`. Auth header on every call: `Authorization: Bearer $SEO_LADDERS_API_KEY`. Target a specific site with `?site=<domain>` or an `X-Site: <domain>` header (defaults to your active site).

---

## Skill or MCP — what's the difference?

- **Skill** = the **brain**: the proper AI-SEO process plus the slash commands. Works in Claude Code and the Claude app.
- **MCP** = the **hands**: the same operations exposed as auto-discovered, authenticated tools. Works in any MCP client.

Same backend, same data. Use the **Skill** where it's supported (it carries the method, not just the tools); add the **MCP** for cleaner stored auth. In ChatGPT you recreate the Skill by pasting `SKILL.md` into Instructions, and the OpenAPI Action gives it the hands.

---

## Slash Commands

| Command | What it does |
|---|---|
| `/seo-ladders` | Overview, account status, and the proper AI-SEO process |
| `/seo-ladders-setup` | Check the API key + what's connected (CMS / GSC), list your sites |
| `/ai-visibility` | AI-visibility score across engines (+ sub-views: citations, sentiment, sources) |
| `/content-gaps` | Buyer questions where AI doesn't name you — what to write to win AI answers |
| `/prompts` | List, add, and swap the prompts you track (incl. GSC-derived); shows your cap |
| `/actions` | Fetch prioritized recommendations (outreach, Reddit, content gaps) |
| `/competitors` | Track competitors for AI share-of-voice (5 slots) — list, promote suggestions, add, remove |
| `/rankings <domain>` | Keywords a domain ranks for on Google (yours or a competitor) |
| `/gsc-audit <domain>` | Full SEO audit (health, CTR, decay, page-2, issues) |
| `/content-radar` | Pull every page from GSC, flag decline/stuck/buried, route to refresh or optimize |
| `/keyword-research [seed]` | Keyword ideas — manual (give a seed) or "find keywords for me" (auto, DR-matched) |
| `/competitor-gap` | Keywords your competitors rank for that you don't — your SEO content gap |
| `/write-article <keyword>` | Research, write, link, backlink, and publish one article |
| `/publish <article-id>` | Publish a generated draft to your connected CMS |
| `/optimize` | Rewrite pages stuck on page 2+ using GSC data |
| `/content-refresh` | Find and refresh articles whose rankings are decaying |
| `/backlinks` | Check exchange membership, credits, and link targets |
| `/content-calendar` | List, schedule, and manage AutoBlog articles |
| `/posts` | Your generated/published articles — content inventory (avoid re-covering topics) |
| `/knowledge` | List/add knowledge facts that ground article writing (optional — site is already scraped) |

Command files live in `seo-ladders/commands/`. If they're not auto-registered by your installer, run `/seo-ladders-setup` or copy `commands/*.md` into your project's `.claude/commands/` folder.

## Plans

| Plan | Price | What you get |
|---|---|---|
| **Pro (trial)** | 3-day free trial | Full access to everything below |
| **Pro** | $99/mo, per website — **50% off your first month** | 20 articles/mo · 30 tracked AI prompts · 100 keyword searches/mo · AI visibility, citations & sentiment · Content Radar · Backlink Exchange · site audits · auto-publish |

The API (this skill + MCP) is included in Pro — not a separate add-on. Current pricing is at [seoladders.com/pricing](https://seoladders.com/pricing). See `seo-ladders/references/plans-and-backlinks.md` for detail.

## Commands (raw API)

```bash
# Your sites
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/projects | jq .

# AI visibility across engines (+ sub-views: citations, sentiment, sources)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility | jq '{visibilityScore, perEngine, shareOfVoice}'
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/content-gaps | jq '.gaps[]'   # AI content gaps

# Competitor gap — keywords competitors rank for that you don't (SEO content gap)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{"competitors":["competitor.com"]}' https://www.seoladders.com/api/v1/keywords/competitor-gap | jq '.keywords[]'

# Audit (async → poll the job) + Content Radar
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{}' https://www.seoladders.com/api/v1/audit | jq '{jobId, status}'
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar | jq '.rows[] | {url, bucket, action}'

# Keyword research — manual seed, or find-for-me (empty body)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{"seed":"ai seo tools","filterByDR":true}' https://www.seoladders.com/api/v1/keywords/search | jq '.keywords[]'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{}' https://www.seoladders.com/api/v1/keywords/discover | jq '{seed, keywords}'

# Write + publish an article (async → poll the job)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{"keyword":"best ai seo tools"}' https://www.seoladders.com/api/v1/articles | jq .

# Backlink exchange · calendar · actions
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/backlinks | jq '{membership, balance}'
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/calendar | jq '.entries[]'
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/actions | jq '.actions[]'

# --- Automation (opt-in — CONFIRM with the user before enabling any of these) ---
# Publish one generated draft now (must be "ready") · autofill · auto-publish · join exchange
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/ARTICLE_ID/publish | jq '{published, url}'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{"enabled":true}' https://www.seoladders.com/api/v1/autoblog/autofill | jq '{autofill, message}'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{"enabled":true}' https://www.seoladders.com/api/v1/settings/auto-publish | jq '{autoPublish, message}'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{}' https://www.seoladders.com/api/v1/backlinks/join | jq '{joined, membership}'
```

Full endpoint reference + every command's curl lives in `seo-ladders/SKILL.md`. The MCP server exposes a tool for each of these — same capabilities, auto-discovered.

## AI Visibility (the differentiator)

This is what classic "AI SEO" skills don't do. On a schedule (and on demand from the dashboard), SEO Ladders asks ChatGPT, Perplexity, Gemini, Claude, Google AI Overview, and Google AI Mode the buyer questions you track, then measures:

- **Visibility score** + per-engine mention rate
- **Share of voice** — you vs. each competitor (5 tracked slots)
- **Sentiment** — how positively AI frames you
- **Citations** — the sources AI pulls from (earn links where `owned:false`)
- **Content gaps** — buyer questions where AI doesn't name you → write those next (`/content-gaps`)

Pull it all with `/ai-visibility` and its sub-views (`/ai-visibility/citations`, `/ai-visibility/content-gaps`, `/ai-visibility/sentiment`, `/ai-visibility/sources`). See `seo-ladders/references/ai-visibility-playbook.md`.

## Backlink Exchange

Earn real backlinks that lift your domain authority through a DR-weighted exchange network. Give links in your articles to earn links back. Check membership, credits, and targets with `/backlinks`; join at the dashboard. See `seo-ladders/references/plans-and-backlinks.md`.

## References

- `seo-ladders/references/ai-visibility-playbook.md` — grow how AI recommends you
- `seo-ladders/references/audit-playbook.md` — the audit-first workflow
- `seo-ladders/references/onboarding-guide.md` — first-run setup + API key
- `seo-ladders/references/plans-and-backlinks.md` — plan detail + exchange mechanics
