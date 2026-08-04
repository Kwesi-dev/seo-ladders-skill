# /content-radar

Pull every page from Google Search Console, flag what's declining/stuck/buried, and route each to optimize. Needs GSC.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar \
  | jq '{connected, count, rows: [.rows[] | {url, primaryKeyword, position, impressions, clicks, ctr, bucket, action, positionDrop, trend: (.history // [] | map(.position))}]}'
```

## How to read it

- **`connected`** — `false` means GSC isn't connected. Tell the user to connect Google Search Console at the dashboard; without it Content Radar has nothing to read.
- `rows` — `[{url, primaryKeyword, position, impressions, clicks, ctr, source, blogPostId, bucket, action, positionDrop?, history?}]`. `history` is `[{date, position}]` — the page's weekly avg-position trend (lower = better), so you can see whether it's improving or slipping over time.
- **`bucket`** — listed worst-first, which is also the order to work them:
  - `dead_with_demand` — the URL returns 404/410 but Google **still shows it** and it's still earning impressions. Traffic already won, landing on an error page. Highest priority: everything else is an improvement, this one is a leak.
  - `declining` — was ranking higher and has slipped for weeks without recovering (see `positionDrop`).
  - `striking_distance` — **stuck** around positions 5–20 and no longer climbing. Pages still moving up on their own are deliberately excluded — they're already fixing themselves.
  - `low_ctr` — ranks on page 1 but earns **under half the clicks normal for its position**. A title/meta problem, not a content one. (Position-relative, not a flat CTR threshold — 2% is fine at #9 and terrible at #2.)
  - `page_two_plus` — stuck on page 2 (positions ~21–40), never reached page 1.
  - `underperforming` — buried past position 40 for 6+ months. **Not an optimize candidate** — a rewrite rarely rescues a page this deep; the fix is usually merge or prune.
- **`action`** — the verdict:
  - `optimize` — an article to rewrite from GSC data (`/optimize`). Covers articles *we* generated (by `blogPostId`) **and pre-join blogs/articles you wrote before joining** (by the page `url`), including decaying articles that already rank — optimize refreshes them in place.
  - `write` — a dead **article** URL worth rebuilding. The rewrite republishes at the *same* URL so the impressions Google already holds for it carry over. Only appears when nothing else of theirs already covers the query.
  - `recommend` — advice rather than a rewrite. Covers **non-article pages** (`/pricing`, `/features`, `/docs`), **dead pages** (rebuild-or-redirect decision), and **buried pages** (merge/prune decision) → fetch the checklist below.
- **`redirectTo`** — dead rows only. When present, a live page of theirs already ranks for that query, so the page **moved** rather than died: redirect to it. Never write a replacement in that case — you'd put two of their own pages against one query.
- **`source`** — `internal` (we generated it) vs `external` (pre-join / not ours).

> **Homepage is excluded.** The root/homepage (e.g. `https://site.com/`) is left out of the radar automatically — it's a brand/navigational page that ranks for brand terms, not actionable content. If a user asks why their homepage isn't listed, that's why; it's not a bug.

## Getting recommendations for an external page

For rows with `action: "recommend"`, fetch a concrete on-page checklist:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com/pricing","keyword":"best crm pricing","bucket":"page_two_plus","position":24}' \
  https://www.seoladders.com/api/v1/content-radar/recommendations | jq '.recommendations[]'
```

Returns `{ recommendations: [{ title, detail, priority }] }`. Pass the row's `url`, `keyword`, `bucket`, and `position` — **the checklist changes with the bucket**:

- most buckets → on-page fixes (title/meta, headings, content gaps, internal links, schema);
- `dead_with_demand` → starts with the rebuild-vs-redirect decision, and never suggests editing a page that doesn't exist;
- `underperforming` → starts with merge / prune / rebuild, and deliberately does **not** suggest title tweaks, which don't move a page from #70.

## What to do with the result

- `optimize` → `/optimize`; `write` → rebuilds the dead article at its original URL; `recommend` → fetch the checklist above and walk the user through the fixes.
- Priority order: `dead_with_demand` first (traffic already earned, currently lost), then `declining` with the largest `positionDrop`, then `striking_distance` (fastest wins).
- Do **not** send `underperforming` rows to `/optimize`. Positions 5–20 are where refresh work pays; past 40 the evidence favours consolidating or pruning instead.
- This is the audit-first flow. See `references/audit-playbook.md`.
