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
| `dead_with_demand` | 404s, but Google still shows it and it still earns impressions | **write** (dead article) or **recommend** (page, or content that moved) |
| `declining` | Was ranking higher, slipping for weeks without recovery (`positionDrop`) | **optimize** |
| `striking_distance` | **Stuck** at positions 5–20 — close to page 1 and no longer climbing | **optimize** |
| `low_ctr` | On page 1 but earns under half the clicks normal for its position — title/meta problem | **optimize** |
| `page_two_plus` | Stuck on page 2 (21–40), never reached page 1 | **optimize** |
| `underperforming` | Buried past position 40 for 6+ months | **recommend** — merge or prune, *not* a rewrite |

Trust the **`action`** field — it's the verdict: `optimize`, `write` or `recommend`.

Two things the buckets deliberately exclude, so don't treat them as gaps:
- pages still **climbing** through the striking-distance band — they're fixing themselves, and rewriting mid-climb risks the progress;
- dead URLs whose content simply **moved** — a live page of theirs already ranks for the query, so `redirectTo` is set and the fix is a redirect, never a second article.

## Step 3 — Route the work

- **`action: optimize`** → page-2 / low-CTR / decaying page → `/optimize` (`POST /optimizations {url}`). It rewrites using the page's real GSC queries, and refreshes articles that already rank in place. Pass the row's `bucket` — it changes the scope of the rewrite (a `low_ctr` page gets its snippet fixed, not its body).
- **`action: write`** → a dead article worth rebuilding at its original URL, so the impressions Google already holds carry over.
- **`action: recommend`** → fetch the checklist. For `dead_with_demand` it leads with rebuild-vs-redirect; for `underperforming`, with merge / prune / rebuild.

Prioritize: `dead_with_demand` first (traffic already earned, currently landing on an error), then the biggest `positionDrop` in `declining` (stop the bleeding), then `striking_distance` (fastest wins), then `low_ctr` (cheap title/meta fixes), then `page_two_plus`. Leave `underperforming` for a consolidation pass — positions 5–20 are where refresh work pays.

## Step 4 — Only then write new content

With the site healthy and existing pages routed, write into the gaps:

- `/rankings competitor.com` → keywords rivals own that you don't.
- `/keyword-research <seed>` → winnable, DR-matched keywords.
- `/write-article <keyword>` → publish, with internal links.

## The rule

Optimize what you already have **before** writing new. Existing pages with impressions are the cheapest, fastest gains — a page in striking distance beats a brand-new article every time.
