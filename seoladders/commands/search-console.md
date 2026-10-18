# /search-console [question]

**Your own Google Search Console data, for any analysis you want.** Clicks, impressions, CTR and position by query, page, date, country, device or search appearance, over any window up to about 16 months, with filters. It comes live from your connection, costs nothing against your plan, and each exact query is cached for 6 hours, so asking again is instant.

Use it for anything: what you rank for, which pages lost clicks month over month, mobile vs desktop, a country, branded vs non-branded, one page's queries. Build the query from what the user asks.

```bash
# Top queries, last 90 days
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/search-console?dimension=query&days=90&limit=100" \
  | jq '{dimension, days, count, rows: [.rows[] | {query, clicks, impressions, ctr, position}]}'

# Top pages instead
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/search-console?dimension=page&days=28&limit=50" | jq .
```

**Simple params:** `dimension=query|page` (default `query`), `days` (default `90`), `limit` (default `100`).

## Any query (flexible mode)

Pass any of these and the route switches to flexible mode (GET params, or a POST JSON body):

| Param | Values |
|---|---|
| `dimensions` | comma list of `query`, `page`, `date`, `country`, `device`, `searchAppearance` |
| `startDate`, `endDate` | `YYYY-MM-DD`; or `days` (max ~486) |
| `filters` | JSON array of `{dimension, operator, expression}`; operators `equals`, `notEquals`, `contains`, `notContains`, `includingRegex`, `excludingRegex`; combined with AND. Countries are 3-letter lowercase (`usa`), devices `DESKTOP`/`MOBILE`/`TABLET` |
| `searchType` | `web` (default), `image`, `video`, `news`, `discover`, `googleNews` |
| `rowLimit`, `startRow` | up to 25000 rows a call; page with `nextStartRow` |
| `aggregationType`, `dataState` | `auto`/`byPage`/`byProperty`; `final`/`all` |

```bash
# One page's queries on mobile, last 28 days
curl -s -G -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/search-console \
  --data-urlencode 'dimensions=query' --data-urlencode 'days=28' \
  --data-urlencode 'filters=[{"dimension":"page","operator":"contains","expression":"/pricing"},{"dimension":"device","operator":"equals","expression":"MOBILE"}]' \
  | jq '{startDate, endDate, count, rows: .rows[:20]}'

# Daily clicks for the whole site
curl -s -G -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/search-console \
  --data-urlencode 'dimensions=date' --data-urlencode 'startDate=2026-07-01' --data-urlencode 'endDate=2026-09-30' | jq '.rows'
```

(MCP: `search-console` with the same fields.) Flexible rows are flat: `{query, page, ..., clicks, impressions, ctr, position}`.

## Trends from stored snapshots (no Google call)

```bash
curl -s -G -H "Authorization: Bearer $SEO_LADDERS_API_KEY" https://www.seoladders.com/api/v1/search-console/history \
  --data-urlencode 'type=page' --data-urlencode 'url=/blog/' | jq '.series[:5]'
```

(MCP: `search-console-history`.) Weekly points per page or query. Each point has a `kind`: `rolling_90d` (90-day totals) or `daily_backfill` (a single day). Compare clicks and impressions within one kind only; position compares across both.

## Not connected or expired

A `400 gsc_not_connected` (or `gsc_reconnect_required`) comes with `connectUrl` and `instructions_for_agent`: tell the user, give the link, don't retry. A `429 gsc_quota_exceeded` means wait about 10 minutes. Never fall back to estimated data presented as measured.

## How to read it

- **`ctr`** is a fraction (`0`–`1`), not a percentage. `0.043` is 4.3% — format it before showing anyone.
- **`position`** is the *average* position over the window, so it hides volatility. A query averaging 8.4 may have swung between 4 and 20.
- **Impressions with near-zero clicks** at position 1–10 is a title/meta problem, not a ranking problem.
- **Position 5–20 with real impressions** is striking distance — the highest-leverage work on the site. Send these to `/optimize`.
- Past position ~40, refreshing rarely pays; consolidate or prune instead.

Short windows are noisy. Use `days=28` to spot a recent change, `days=90` to decide anything.

## What to do with the result

- Pair with `/content-radar`, which buckets these same rows into a prioritised action list — prefer that when the user wants "what should I do", and use this command when they want the raw numbers or a window the radar doesn't cover.
- Queries here are the best seed for AI prompts (`/prompts`) — people ask AI what they already type into Google.
- Pages ranking well that AI never cites is exactly the gap `/link-building` closes.
- See `references/audit-playbook.md`.
