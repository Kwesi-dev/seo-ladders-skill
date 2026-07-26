# /rankings <domain>

Keywords a domain ranks for on Google — yours or a competitor's.

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
- **A competitor's domain** → keywords they rank for that you don't are content gaps → `/keyword-research` then `/write-article`.
- Default `limit` is 50; raise it for a fuller picture. Add `?site=<domain>` to scope to a specific project.
