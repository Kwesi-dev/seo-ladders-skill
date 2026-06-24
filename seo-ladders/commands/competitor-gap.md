# /competitor-gap

The keywords your competitors rank for that **you don't** — your content gap. Hand the winnable ones to `/write-article`.

`$ARGUMENTS` = competitor domain(s) to analyze (up to 3). If empty, use the competitors you already track (`/competitors`) or ask the user.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"competitors":["competitor.com","rival.io"]}' \
  https://www.seoladders.com/api/v1/keywords/competitor-gap \
  | jq '{count, competitorsAnalyzed, yourRankingCount, keywords: .keywords}'
```

- Body: `{ competitors: string[] (max 3) }`.
- Returns `{ keywords, count, competitorsAnalyzed, stats, yourRankingCount, usage }` — `keywords` are the gap (they rank, you don't), with volume/difficulty.

## What to do with the result

- Filter to keywords your domain rating can realistically win, then `/write-article` them (or schedule via `/content-calendar`).
- This is the content gap that matters for SEO. For *AI*-side gaps (where AI doesn't mention you), use `/ai-visibility`.
- Counts against your keyword-research usage.
