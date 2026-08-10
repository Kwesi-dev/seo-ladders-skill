# /search-console [query|page]

The queries and pages the site **actually** ranks for, straight from Google Search Console — clicks, impressions, CTR and average position. This is the source of truth the site audit and prompt discovery both build on, so reach for it whenever a claim about current performance needs grounding in real data rather than estimates.

```bash
# Top queries, last 90 days
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/search-console?dimension=query&days=90&limit=100" \
  | jq '{dimension, days, count, rows: [.rows[] | {query, clicks, impressions, ctr, position}]}'

# Top pages instead
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/search-console?dimension=page&days=28&limit=50" | jq .
```

**Params:** `dimension=query|page` (default `query`), `days` (default `90`), `limit` (default `100`).

**Requires GSC to be connected.** A `400` means it isn't — tell the user to connect Google Search Console at `/dashboard`. Don't fall back to estimated data and present it as measured.

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
