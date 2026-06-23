# Audit Playbook — audit before you write anything

Don't write new content into a site that's leaking. Audit first, fix what's slipping, *then* publish. New articles compound only on a healthy base.

## Step 1 — Run the audit

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"domain":"example.com"}' \
  https://www.seoladders.com/api/v1/audit | jq '{jobId, status}'

# Poll until completed
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/jobs/JOB_ID | jq '{status, output}'
```

Or read the latest stored audit without waiting:

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/audit | jq '.audit'
```

Read `health_score`, `total_issues`, `total_pages_crawled`. Fix technical blockers (broken links, missing meta, thin/duplicate pages) before anything else.

## Step 2 — Run Content Radar

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/content-radar \
  | jq '.rows[] | {url, primaryKeyword, position, ctr, bucket, action, positionDrop}'
```

`connected:false` → connect Google Search Console first; Content Radar reads GSC.

### Read the buckets

| Bucket | Meaning | Route to |
|---|---|---|
| `declining` | Losing position over time (`positionDrop`) | **refresh** |
| `striking_distance` | Just off page 1 — a nudge wins it | **optimize** |
| `low_ctr` | Ranks fine, few clicks — title/meta problem | **optimize** |
| `page_two_plus` | Buried on page 2+ | **optimize** |
| `underperforming` | Impressions without matching clicks/position | refresh or optimize (read `action`) |

Trust the **`action`** field — it's the verdict: `refresh` or `optimize`.

## Step 3 — Route the work

- **`action: refresh`** → article already ranks but is decaying → `/content-refresh` (or `POST /refreshes`). Confirm with `GET /refresh-candidates`.
- **`action: optimize`** → page-2 / low-CTR page that never broke through → `/optimize` (`POST /optimizations {url}`). It rewrites using the page's real GSC queries.

Prioritize: biggest `positionDrop` in `declining` (stop the bleeding), then `striking_distance` (fastest wins), then `low_ctr` (cheap title/meta fixes), then `page_two_plus`.

## Step 4 — Only then write new content

With the site healthy and existing pages routed, write into the gaps:

- `/rankings competitor.com` → keywords rivals own that you don't.
- `/keyword-research <seed>` → winnable, DR-matched keywords.
- `/write-article <keyword>` → publish, with internal + backlink-exchange links.

## The rule

Refresh and optimize what you already have **before** writing new. Existing pages with impressions are the cheapest, fastest gains — a page in striking distance beats a brand-new article every time.
