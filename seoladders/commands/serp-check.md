# /serp-check <keyword>

What Google shows for one keyword right now, measured against **your** site. **Keyword** = `$ARGUMENTS`.

```bash
# Top 10, SERP features, People Also Ask
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"$ARGUMENTS"}' \
  https://www.seoladders.com/api/v1/serp | jq '{results, features, hasAiOverview, peopleAlsoAsk}'

# Where your site ranks in the top 100
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"$ARGUMENTS","check":"rank"}' \
  https://www.seoladders.com/api/v1/serp | jq '{position, matches, leaders}'

# Google's AI Overview and AI Mode: what they say, who they cite, whether you're cited
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"$ARGUMENTS","check":"ai-answers"}' \
  https://www.seoladders.com/api/v1/serp | jq '{overview, aiMode}'
```

(MCP: `serp-check` with `check`.)

- Always your own site: there is no domain to pass.
- **Metered:** `rank` uses the rank check allowance; `top10` and `ai-answers` use keyword research. Both are the dashboard's own allowances. A result from the last day (three days for AI answers) is served from cache for free.
- For keywords you already rank for, `/search-console` has real positions from Google at no cost. Check it first.

## What to do with the result

- `top10`: what format wins (listicles, guides, tools), who holds the page, and the People Also Ask questions to answer in an article.
- `rank`: no position in the top 100 means there's no page for it yet; schedule one.
- `ai-answers`: if you're not cited, the cited pages are the ones to beat, or to pitch through `/link-building`.
