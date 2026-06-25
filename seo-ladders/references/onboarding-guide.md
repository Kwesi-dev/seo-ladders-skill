# Onboarding Guide — first run

Get from zero to a working skill in five steps. Most of this happens once, on the dashboard.

## 1. Sign up

Go to **[seoladders.com](https://www.seoladders.com)** and create an account. A **3-day free trial** is available — full access, no commitment.

## 2. Onboarding (we scrape the site)

During onboarding you give us your website URL and we **scrape it to learn the business** — brand, audience, products, and competitors. No manual knowledge entry. This is what powers keyword matching, prompt discovery, and the AI-visibility monitor, so let it finish.

## 3. Connect the website/CMS + Google Search Console

Two connections matter most:

- **Website / CMS** — so the skill can publish articles (WordPress or any webhook CMS).
- **Google Search Console** — powers the **audit, Content Radar, rankings, and prompt discovery**. Without GSC, Content Radar and GSC-derived prompts run blind, and calls return `400` with `details.code: gsc_not_connected`.

Connect both in the dashboard before running the SEO loop.

## 4. Get the API key

Go to **Dashboard → Developers** and create an API key (`sk_live_...`). The same key works for both this curl skill and the hosted MCP server.

## 5. Export it

```bash
export SEO_LADDERS_API_KEY=sk_live_your_key_here
```

## Verify

```bash
# Key works + lists your sites
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/projects | jq '{count, projects:[.projects[]|{name,domain}]}'

# GSC connected?
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/search-console?dimension=query&days=90&limit=5" | jq .
```

- `401 unauthorized` → key wrong/missing; recheck the export.
- `402 subscription_required` → start a plan/trial; surface `action.url` verbatim.
- `400 gsc_not_connected` → connect Google Search Console (step 3).

## Then

Run the loop: `/seo-ladders` (orientation) → `/gsc-audit` + `/content-radar` (audit first) → `/ai-visibility` (the differentiator) → `/prompts` → `/keyword-research` → `/write-article` → `/optimize` / `/content-refresh` → `/actions`.
