# /rankings <domain>

Keywords a domain ranks for on Google — yours or a competitor's.

**For your own site, read what the agent already tracks first (free):**

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/rankings/tracked?limit=50" \
  | jq '{summary, keywords: [.keywords[] | {keyword, position, change, best, url}], articles: .articles[:10]}'
```

(MCP: `tracked-rankings`; `filter` = declining | improving | top10 | striking.) That's the Rank Tracking page: tracked keywords with position, change, best and history, plus each article's weekly Search Console position. The live lookup below is metered (keyword research allowance), so use it for a competitor's domain or when the user asks for fresh numbers.

**Domain** = `$ARGUMENTS` (e.g. `competitor.com`). If `$ARGUMENTS` is empty, omit `domain=` to use your active site.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/rankings?domain=$ARGUMENTS&limit=50" \
  | jq '{domain, totalCount, itemsCount, keywords: [.keywords[] | {keyword, position, url, searchVolume, keywordDifficulty, intent}]}'
```

## How to read it

- `keywords` — `[{keyword, position, url, searchVolume, keywordDifficulty, intent, ...}]`.
- `totalCount` — total keywords the domain ranks for; `itemsCount` — how many returned (capped by `limit`).

## What to do with the result

- **Your domain** → surface page-2 keywords (`position` 11–20) as `/optimize` targets.
- **A competitor's domain** → keywords they rank for that you don't are content gaps → `/keyword-research` then `/content-calendar`.
- Default `limit` is 50, maximum 100. Add `?site=<domain>` to scope to a specific project.

## Metered

Each call spends **one keyword-research credit** from the monthly allowance it shares with `/keyword-research` (Pro: 100/mo). A `429` means the allowance is spent; retrying will not help. Look up the one or two domains that matter, and confirm with the user before checking more. Never loop over a list of domains.
