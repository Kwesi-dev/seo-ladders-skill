# SEOLadders — AI Search + SEO Skill

AI-search + SEO for any AI agent. SEOLadders provides the infrastructure: real keyword data matched to your domain rating, a Google Search Console audit, full article writing + CMS publishing, a content calendar — and, uniquely, the **AI-visibility loop**: it measures whether ChatGPT, Perplexity, Gemini, Claude, and Google AI actually recommend your brand, finds the prompts and citation sources that matter, and tells you exactly what to fix.

Most "AI SEO" skills stop at writing articles. This one also measures whether AI is recommending you — and closes the gap.

---

## 1. How it works — the proper AI-SEO process

This is the full loop the skill runs (and walks you through the first time it loads). Steps 1–2 are your one-time setup — that's where you create the API key the **Install** section below needs.

1. **Sign up and onboard** at [seoladders.com](https://www.seoladders.com) — we scrape the site to learn the brand, audience, and competitors. A **3-day free trial** is available, and the first month is **50% off**.
2. **Connect** the two things that matter most: the website/CMS and Google Search Console. Then create your API key at **Dashboard → Developers** (`sk_live_...`).
3. **Audit before writing anything** (`/gsc-audit` + `/content-radar`) — find what's slipping, stuck, or buried.
4. **Check AI visibility** (`/ai-visibility`) — are you in the answer when buyers ask ChatGPT/Perplexity/Gemini/Claude/Google AI? Find the gaps and the sources AI cites.
5. **Build topic clusters** (`/topical-authority`) — a pillar topic that holds *both* the keywords to rank for on Google *and* the AI prompts to win in AI answers. Research keywords to fill each cluster (`/keyword-research`), and track the buyer prompts under it.
6. **Track the right prompts** (`/prompts`) — pull suggestions from your real Google Search Console queries, keywords, or People-Also-Asked, then add the good ones under the relevant topic. Respect the cap; swap low-value prompts when full.
7. **Choose how to ship** — either write now (`/write-article`), or schedule the keywords on the content calendar (`/content-calendar`) and turn on **autofill + auto-publish** so AutoBlog generates and publishes them for you (opt-in autopilot — confirm with the user before enabling either).
8. **Write and publish** (`/write-article`). Every article ships with internal links, AI images, YouTube embeds, and citations.
9. **Optimize** pages stuck on page 2+, declining, or going stale (`/optimize`) — it also refreshes content in place.
10. **Act on the recommendations** (`/actions`) — outreach, Reddit, and content gaps from your real data.
11. **Link building** (`/link-building`) — the pages AI cites for your prompts, plus the pages linking to your competitors and not to you, quality-scored, with a discovered contact and a drafted, ready-to-send pitch. You review and send; never auto-sent.

Full setup walkthrough: `seoladders/references/onboarding-guide.md`.

---

## 2. Install — add the Skill *and* connect the MCP

For the best experience, do **both** — in whatever app you use:

- **Skill** → teaches Claude *how* SEOLadders works: the method, the commands, when to use each. This is what makes Claude actually understand the platform instead of guessing.
- **MCP** → gives Claude the authenticated tools to *run* everything.

The same `sk_live_...` key works for both. Set it up for your app:

### Claude app  (web + desktop)

Add it as a **Skill**. The app uploads skills as a small `.zip`, and there are two ways to get one — the first never sends you to GitHub.

**Option A — ask Claude to package it (easiest)**

Paste this into any Claude conversation:

> Package the skill at https://github.com/Kwesi-dev/seo-ladders-skill.git into a zip I can upload — the `seoladders` folder, with `SKILL.md` at its root.

Claude clones the repo, zips the right folder and hands the file back to download. That's the only download involved: no visiting GitHub, no unzipping a whole repo, and no risk of zipping the wrong level — which is the mistake that makes the upload fail.

Then upload it — **Customize → Skills → +** (on some versions **Settings → Capabilities → Skills → Upload skill**), pick the zip, and provide your API key when asked. It appears under **Personal skills** with a *slash command + auto* trigger, so Claude runs `/ai-visibility`, `/write-article`, `/gsc-audit` and the rest.

**Option B — make the zip yourself**

**Step 1 — get the skill folder**

On this repo's GitHub page, click the green **Code** button → **Download ZIP**, then unzip it. Inside you'll find a folder called **`seoladders`** (it holds `SKILL.md`, `commands/`, and `references/`).

**Step 2 — zip just the `seoladders` folder**

- **Mac:** right-click the `seoladders` folder → **Compress "seoladders"** → you get `seoladders.zip`.
- **Windows:** right-click the `seoladders` folder → **Send to → Compressed (zipped) folder**.

> Zip the **`seoladders` folder itself** (the one with `SKILL.md` inside) — not the whole repo.

**Step 3 — upload it to Claude**

1. **Customize → Skills → +** (add a personal skill).
2. Upload `seoladders.zip`.
3. It appears under **Personal skills** with a *slash command + auto* trigger; Claude runs `/ai-visibility`, `/write-article`, `/gsc-audit`, etc.
4. Provide your API key when asked.

**Then add the MCP connector — this is what actually runs the commands in the Claude app.** The app's sandbox can't reach the API with raw `curl` (network egress is locked down and `jq` isn't installed), so the skill executes through the MCP tools instead. The connection runs server-side — nothing to install, nothing blocked:

1. **Customize → Connectors → + → Add custom connector**.
2. **Name:** `SEOLadders`
3. **Remote MCP server URL:** `https://www.seoladders.com/api/mcp?key=sk_live_...` — put your key right in the URL. The web dialog has no header field, so the key goes here. (It's your own key, stored in your own connector settings.)
4. Leave the OAuth fields blank → **Add**.

> The **Skill** gives Claude the process; the **MCP connector** gives it execution. In the Claude app you want **both**. (In Claude Code / Cursor you instead put the key in the config `headers` — see below.)

### Claude Code  (or Cursor / Windsurf / Codex)

**1) Install the skill** — the understanding + slash commands:

```bash
npx skills add Kwesi-dev/seo-ladders-skill/seoladders
```

**2) Connect the MCP server** — the tools. Add to your MCP config:

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

Then type `/seoladders-setup` to confirm everything's connected.

### ChatGPT

Build a **Custom GPT** that calls the API:

1. **Create a GPT → Configure → Actions → Import from URL** → `https://www.seoladders.com/api/openapi.json`
2. **Authentication → API Key → Bearer** → paste your `sk_live_...`
3. Paste `seoladders/SKILL.md` into the GPT's **Instructions** (this gives it the process + commands).
4. Ask it to "check my AI visibility", "audit my site", or "write an article for &lt;keyword&gt;" — it runs the real calls.

> **Per-user keys:** a *published* GPT with API-Key auth uses one key for everyone. So each user should create their **own** Custom GPT with their own key — or, on ChatGPT Pro/Business, add the MCP server (`/api/mcp`) as a custom connector (per-user auth).

### Any terminal / other AI

Export the key and use the `curl` commands (see **Commands (raw API)** below) — works anywhere, no install:

```bash
export SEO_LADDERS_API_KEY=sk_live_...
```

Base URL `https://www.seoladders.com/api/v1`. Auth header on every call: `Authorization: Bearer $SEO_LADDERS_API_KEY`. Target a specific site with `?site=<domain>` or an `X-Site: <domain>` header (defaults to your active site).

---

## Why both Skill + MCP?

- **Skill = the brain.** The proper AI-SEO process and the commands — *how* and *when* to use SEOLadders. Without it, Claude has tools but no strategy (it'd write before auditing, skip the AI-visibility loop, miss the GEO angle, run automation without asking).
- **MCP = the hands.** The same operations as authenticated, auto-discovered tools that actually execute — and they run server-side, so they work even in apps where raw `curl` can't (like the Claude web app).

They're complementary, not either/or — add **both** so Claude *understands* SEOLadders and can *run* it. In ChatGPT you get the same pairing by pasting `SKILL.md` into Instructions (brain) + the OpenAPI Action (hands).

---

## Slash Commands

Run any command by name — e.g. `/link-building`, `/ai-visibility`. If your app namespaces skill commands (Claude Code / plugins), they appear under the skill as `/seoladders:<command>` (e.g. `/seoladders:link-building`, `/seoladders:gsc-audit`). Both forms invoke the same command.

| Command | What it does |
|---|---|
| `/seoladders` | Overview, account status, and the proper AI-SEO process |
| `/seoladders-setup` | Check the API key + what's connected (CMS / GSC), list your sites |
| `/ai-visibility` | AI-visibility score across engines (+ sub-views: citations, sentiment, sources) |
| `/content-gaps` | Buyer questions where AI doesn't name you — what to write to win AI answers |
| `/prompts` | List, add, and swap the prompts you track (incl. GSC-derived); shows your cap |
| `/actions` | Fetch prioritized recommendations (outreach, Reddit, content gaps) |
| `/link-building` | Pages AI cites for you **and** pages linking to your competitors but not you — quality-scored, with a contact and a drafted pitch; list, find/refresh, update, follow-up |
| `/competitors` | Track competitors for AI share-of-voice (5 slots) — list, promote suggestions, add, remove |
| `/rankings <domain>` | Keywords a domain ranks for on Google (yours or a competitor) |
| `/gsc-audit <domain>` | Full SEO audit (health, CTR, decay, page-2, issues) |
| `/content-radar` | Pull every page from GSC, flag decline/stuck/buried, route to optimize |
| `/search-console` | Raw GSC rows — the queries or pages you actually rank for, with clicks, impressions, CTR, position |
| `/indexing` | Which pages Google actually knows about — sitemap vs crawl vs impressions, and Google's own verdict (quota-gated) |
| `/keyword-research [seed]` | Keyword ideas — manual (give a seed) or "find keywords for me" (auto, DR-matched) |
| `/competitor-gap` | Keywords your competitors rank for that you don't — your SEO content gap |
| `/write-article <keyword>` | Research, write, link, and publish one article |
| `/publish <article-id>` | Publish a generated draft to your connected CMS |
| `/optimize` | Rewrite pages stuck on page 2+ using GSC data |
| `/content-calendar` | List, schedule, and manage AutoBlog articles |
| `/posts` | Your generated/published articles — content inventory (avoid re-covering topics) |
| `/knowledge` | List/add knowledge facts that ground article writing (optional — site is already scraped) |

Command files live in `seoladders/commands/`. If they're not auto-registered by your installer, run `/seoladders-setup` or copy `commands/*.md` into your project's `.claude/commands/` folder.

## Plans

| Plan | Price | What you get |
|---|---|---|
| **Pro (trial)** | 3-day free trial | Full access to everything below |
| **Pro** | $99/mo, per website — **50% off your first month** | 20 articles/mo · 30 tracked AI prompts · 100 keyword searches/mo · AI visibility, citations & sentiment · Content Radar · site audits · auto-publish |

The API (this skill + MCP) is included in Pro — not a separate add-on. Current pricing is at [seoladders.com/pricing](https://www.seoladders.com/pricing). See `seoladders/references/plans-and-backlinks.md` for detail.

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

# Calendar · actions
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/calendar | jq '.entries[]'
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/actions | jq '.actions[]'

# --- Automation (opt-in — CONFIRM with the user before enabling any of these) ---
# Publish one generated draft now (must be "ready") · autofill · auto-publish
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/ARTICLE_ID/publish | jq '{published, url}'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{"enabled":true}' https://www.seoladders.com/api/v1/autoblog/autofill | jq '{autofill, message}'
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" -H "Content-Type: application/json" \
  -d '{"enabled":true}' https://www.seoladders.com/api/v1/settings/auto-publish | jq '{autoPublish, message}'
```

Full endpoint reference + every command's curl lives in `seoladders/SKILL.md`. The MCP server exposes a tool for each of these — same capabilities, auto-discovered.

## AI Visibility (the differentiator)

This is what classic "AI SEO" skills don't do. On a schedule (and on demand from the dashboard), SEOLadders asks ChatGPT, Perplexity, Gemini, Claude, Google AI Overview, and Google AI Mode the buyer questions you track, then measures:

- **Visibility score** + per-engine mention rate
- **Share of voice** — you vs. each competitor (5 tracked slots)
- **Sentiment** — how positively AI frames you
- **Citations** — the sources AI pulls from (earn links where `owned:false`)
- **Content gaps** — buyer questions where AI doesn't name you → write those next (`/content-gaps`)

Pull it all with `/ai-visibility` and its sub-views (`/ai-visibility/citations`, `/ai-visibility/content-gaps`, `/ai-visibility/sentiment`, `/ai-visibility/sources`). See `seoladders/references/ai-visibility-playbook.md`.

## References

- `seoladders/references/ai-visibility-playbook.md` — grow how AI recommends you
- `seoladders/references/audit-playbook.md` — the audit-first workflow
- `seoladders/references/onboarding-guide.md` — first-run setup + API key
- `seoladders/references/plans-and-backlinks.md` — plan detail

## Community

Install help, updates, and a place to share ranking wins — **join the Discord**: https://discord.gg/2xfNfCPAHZ
