# /keyword-lookup <keywords>

Volume, difficulty (0-100), CPC and intent for **1 to 10 keywords the user already has in mind**. **Keywords** = `$ARGUMENTS` (comma-separated).

Use it to size up specific keywords. To discover new ones use `/keyword-research`; for the ones the agent already chose use `/my-keywords` (free). If a keyword is already in `/my-keywords`, its volume and difficulty are there, so don't look it up again.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keywords":["ai seo tools","seo for startups"],"country":"US"}' \
  https://www.seoladders.com/api/v1/keywords/lookup | jq '{keywords, charged, remaining}'
```

(MCP: `keyword-lookup`.)

- `country` is a two-letter code and defaults to the site's market.
- **Metered:** one keyword research unit per keyword, from the same monthly allowance as the dashboard. A keyword anyone looked up recently is served from cache and costs nothing. `charged` says how many units this call used.
- If the allowance runs out partway, the call returns what it got plus `stopped`.

## What to do with the result

- Compare difficulty with the site's domain rating: under about 25 for a new site is winnable; higher is a slow climb.
- Good ones belong in a topic cluster (`/topical-authority`, add-keywords) so the agent schedules and writes them.
