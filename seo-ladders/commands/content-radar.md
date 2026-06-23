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
- **`action`** — the verdict: `refresh` (decaying article, update it) or `optimize` (page-2 page, rewrite from GSC data).

## What to do with the result

- Group rows by `action`. Route `refresh` → `/content-refresh`, `optimize` → `/optimize`.
- Prioritize `declining` with the largest `positionDrop` and `striking_distance` (fastest wins).
- This is the audit-first flow. See `references/audit-playbook.md`.
