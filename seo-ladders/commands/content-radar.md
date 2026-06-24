# /content-radar

Pull every page from Google Search Console, flag what's declining/stuck/buried, and route each to refresh or optimize. Needs GSC.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar \
  | jq '{connected, count, rows: [.rows[] | {url, primaryKeyword, position, impressions, clicks, ctr, bucket, action, positionDrop}]}'
```

## How to read it

- **`connected`** — `false` means GSC isn't connected. Tell the user to connect Google Search Console at the dashboard; without it Content Radar has nothing to read.
- `rows` — `[{url, primaryKeyword, position, impressions, clicks, ctr, source, blogPostId, bucket, action, positionDrop?}]`.
- **`bucket`**:
  - `declining` — losing position over time (see `positionDrop`).
  - `striking_distance` — close to page 1, a nudge could break it through.
  - `low_ctr` — ranks fine but few clicks (title/meta problem).
  - `page_two_plus` — buried on page 2+.
  - `underperforming` — impressions without the clicks/position to match.
- **`action`** — the verdict, based on whether we can rewrite the page:
  - `refresh` — a decaying article *we* generated → update it in place (`/content-refresh`).
  - `optimize` — a page-2 article *we* generated → rewrite from GSC data (`/optimize`).
  - `recommend` — an **external** page (a pre-join blog, or a non-article page like `/pricing`, `/features`) we can't auto-rewrite → fetch a manual improvement checklist (below).
- **`source`** — `internal` (we generated it → refresh/optimize) vs `external` (→ recommend).

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
