# /seo-ladders

Overview + the proper AI-SEO process. Confirm the key works and list the user's sites.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/projects | jq '{count, projects: [.projects[] | {name, domain, country}]}'
```

## The proper AI-SEO process (run it in this order)

1. **Onboard** — sign up at seoladders.com; we scrape the site to learn the brand, audience, and competitors.
2. **Connect** the website/CMS and **Google Search Console** (powers audit, Content Radar, rankings, prompt discovery).
3. **Audit first** — `/gsc-audit` + `/content-radar` to find what's slipping, stuck, or buried.
4. **Check AI visibility** (`/ai-visibility`) — are you in the answer on ChatGPT/Perplexity/Gemini/Claude/Google AI?
5. **Track the right prompts** (`/prompts`) — add buyer questions, including GSC-derived ones; respect the cap.
6. **Research keywords** (`/keyword-research`) — pick keywords your domain rating can win.
7. **Write + publish** (`/write-article`) — with internal links + 1–2 backlink-exchange links.
8. **Optimize + refresh** (`/optimize`, `/content-refresh`) — page-2 pages and decaying articles.
9. **Act on recommendations** (`/actions`) — outreach, Reddit, content gaps from real monitoring data.

## What to do with the result

- Show the sites and their domains; ask which one to work on (pass `?site=<domain>` on later calls).
- If `count` is 0 → onboarding isn't finished. Send the user to `/seo-ladders-setup`.
- **401 unauthorized** → bad/missing key; run `/seo-ladders-setup`.
- **402 subscription_required** → surface `action.url` verbatim so they can start a plan/trial.
- Lead with AI visibility — it's the differentiator. After listing sites, suggest `/ai-visibility`.
