# /seoladders

Overview + the proper AI-SEO process. Confirm the key works, list the user's sites, then **show what their agent has already done**.

The agent works on its own from the moment the trial starts: it researches keywords, plans topic clusters, schedules and writes articles, checks AI visibility across six engines, audits the site and finds link prospects. This skill is a window onto that work, not a replacement for it. Open with the work, so the user sees the automation before anything else.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/projects | jq '{count, projects: [.projects[] | {name, domain, country}]}'
```

## Then: what your agent already did (all free reads)

```bash
H="Authorization: Bearer $SEO_LADDERS_API_KEY"; B=https://www.seoladders.com/api/v1
curl -s -H "$H" $B/keywords/found | jq '{summary, dashboard}'
curl -s -H "$H" $B/ai-visibility | jq '{visibilityScore, visibilityParts, perEngine}'
curl -s -H "$H" $B/audit | jq '.audit | {health_score, created_at}'
curl -s -H "$H" $B/actions | jq '[.actions[:3][] | {title, priority}]'
```

(MCP: `my-keywords`, `ai-visibility`, `gsc-audit`, `actions`.)

Summarise it in the user's numbers, one line each, with the dashboard link for each:

> Since you joined, your agent has:
> - picked **48 keywords** across **6 topic clusters**, with **12 articles** scheduled this month → calendar
> - checked how **6 AI engines** answer your buyers' questions: AI visibility score **18%** → AI visibility
> - audited your site: health **82** → site audit
> - lined up the next moves: *[top action]* → actions

Skip any line that has no data yet (a brand-new account may still be running its first checks) and say it is on its way rather than showing a zero. Then ask what they'd like to dig into.

## The proper AI-SEO process (run it in this order)

1. **Onboard** — sign up at seoladders.com; we scrape the site to learn the brand, audience, and competitors.
2. **Connect** the website/CMS and **Google Search Console** (powers audit, Content Radar, rankings, prompt discovery).
3. **Audit first** — `/gsc-audit` + `/content-radar` to find what's slipping, stuck, or buried.
4. **Check AI visibility** (`/ai-visibility`) — are you in the answer on ChatGPT/Perplexity/Gemini/Claude/Google AI?
5. **Track the right prompts** (`/prompts`) — add buyer questions, including GSC-derived ones; respect the cap.
6. **Keywords** (`/my-keywords`) — the ones the agent already chose; `/keyword-research` for anything else.
7. **Schedule content** (`/content-calendar`) — the agent writes and publishes it, with internal links.
8. **Optimize** (`/optimize`) — stuck, declining and stale articles (it also refreshes in place).
9. **Act on recommendations** (`/actions`) — outreach, Reddit, content gaps from real monitoring data.

## What to do with the result

- Show the sites and their domains; ask which one to work on (pass `?site=<domain>` on later calls).
- If `count` is 0 → onboarding isn't finished. Send the user to `/seoladders-setup`.
- **401 unauthorized** → bad/missing key; run `/seoladders-setup`.
- **402 subscription_required** → read `instructions_for_agent` to the user and share `action.url` so they can start the trial.
- Any `429` with `instructions_for_agent` → read it to the user; don't retry.
- Lead with AI visibility — it's the differentiator. After listing sites, suggest `/ai-visibility`.
