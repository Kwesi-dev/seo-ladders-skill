# /content-radar

Pull every page from Google Search Console, flag what's declining/stuck/buried, and route each to refresh or optimize. Needs GSC.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar \
  | jq '{connected, count, rows: [.rows[] | {url, primaryKeyword, position, impressions, clicks, ctr, bucket, action, positionDrop, trend: (.history // [] | map(.position))}]}'
```

## How to read it

- **`connected`** — `false` means GSC isn't connected. Tell the user to connect Google Search Console at the dashboard; without it Content Radar has nothing to read.
- `rows` — `[{url, primaryKeyword, position, impressions, clicks, ctr, source, blogPostId, bucket, action, positionDrop?, history?}]`. `history` is `[{date, position}]` — the page's weekly avg-position trend (lower = better), so you can see whether it's improving or slipping over time.
- **`bucket`**:
  - `declining` — losing position over time (see `positionDrop`).
  - `striking_distance` — close to page 1, a nudge could break it through.
  - `low_ctr` — ranks fine but few clicks (title/meta problem).
  - `page_two_plus` — buried on page 2+.
  - `underperforming` — impressions without the clicks/position to match.
- **`action`** — the verdict:
  - `refresh` — a decaying article *we* generated → update it in place (`/content-refresh`, by `blogPostId`).
  - `optimize` — an article to rewrite from GSC data (`/optimize`). Covers articles *we* generated (by `blogPostId`) **and pre-join blogs/articles you wrote before joining** (by the page `url`).
  - `recommend` — a **non-article page** (e.g. `/pricing`, `/features`, `/docs`, a landing page) that can't be sensibly rewritten as an article → fetch a manual improvement checklist (below).
- **`source`** — `internal` (we generated it) vs `external` (pre-join / not ours). Articles get refresh/optimize either way; only non-article pages get `recommend`.

> **Homepage is excluded.** The root/homepage (e.g. `https://site.com/`) is left out of the radar automatically — it's a brand/navigational page that ranks for brand terms, not actionable content. If a user asks why their homepage isn't listed, that's why; it's not a bug.

## Getting recommendations for an external page

For rows with `action: "recommend"`, fetch a concrete on-page checklist:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"https://example.com/pricing","keyword":"best crm pricing","bucket":"striking_distance","position":14}' \
  https://www.seoladders.com/api/v1/content-radar/recommendations | jq '.recommendations[]'
```

Returns `{ recommendations: [{ title, detail, priority }] }` — specific on-page fixes (title/meta, headings, content gaps, internal links, schema) the user applies manually. Pass the row's `url`, `keyword`, `bucket`, and `position`.

## What to do with the result

- `refresh` → `/content-refresh`; `optimize` → `/optimize`; `recommend` → fetch the checklist above and walk the user through the fixes.
- Prioritize `declining` with the largest `positionDrop` and `striking_distance` (fastest wins).
- This is the audit-first flow. See `references/audit-playbook.md`.
