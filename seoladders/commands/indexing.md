# /indexing [coverage|sync|inspect]

Answer **"which of my pages does Google actually know about?"** — diffs the site's sitemap against our crawl and against the pages Search Console recorded impressions for. Base URL `https://www.seoladders.com/api/v1`. Auth header on every call: `Authorization: Bearer $SEO_LADDERS_API_KEY`.

**Needs a completed site audit first** — the diff runs against a real crawl, not an empty one. A `409` means no audit has finished; run `POST /api/v1/audit` (see `/gsc-audit`) and poll the job before retrying.

## Coverage (free, read-only)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/indexing \
  | jq '.coverage.groups[] | {key, count: (.rows | length)}'
```

Five groups, ordered by how certain the finding is:

| `key` | What it means |
|---|---|
| `broken_in_sitemap` | Sitemap entries that don't resolve. Unambiguously broken — fix first. |
| `never_shown` | In the sitemap, but never appeared in a search result. |
| `missing_from_sitemap` | We crawled it; the sitemap omits it. |
| `ranking_not_in_sitemap` | Earning impressions **despite** being absent from the sitemap. |
| `dropped_from_sitemap` | Was in the sitemap on an earlier read, now gone. |

### The one thing you must not say

**`never_shown` is NOT "not indexed."** A page can be perfectly indexed and simply never searched for — a long-tail post nobody queries looks identical to a page Google refused. Describe this group as *"never shown in a result"* and nothing stronger. Google's actual verdict only comes from `inspect` below.

## Sync — re-read the sitemaps (free)

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"action":"sync"}' \
  https://www.seoladders.com/api/v1/indexing | jq '{sync, coverage: .coverage.groups}'
```

Run this after the user publishes or removes pages, before reading anything into the groups.

## Inspect — ask Google directly (SPENDS QUOTA)

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"action":"inspect","budget":25}' \
  https://www.seoladders.com/api/v1/indexing | jq '.inspection'
```

Calls Google's URL Inspection API on the `never_shown` shortlist and returns **Google's own status strings verbatim** (e.g. `Crawled - currently not indexed`, `Discovered - currently not indexed`) plus any canonical mismatches.

**Always confirm with the user before calling this, and tell them how many URLs you intend to check.**

- The API allows **2,000 checks per day for the entire property** — a shared, limited resource, not a per-agent allowance.
- `budget` defaults to **200** (10% of the daily property quota) and is clamped to **500** max. That default is what gets spent when you omit the argument, so pass a smaller number if you only want a sample.
- Results are **cached 30 days**, so re-running over the same URLs is cheap.
- The shortlist is recomputed server-side — you cannot point the quota at a URL list of your own choosing. That's deliberate.
- `inspection` is always present in the response, so a partial run can never be read as a complete one. Check it before summarising.

## What to do with the result

- Fix `broken_in_sitemap` first — it's the only group with no interpretation required.
- **A canonical mismatch is the tell that resubmitting the sitemap won't help.** Google has chosen a different canonical; the fix is on the page, not in the sitemap.
- `ranking_not_in_sitemap` is a quick win — the page already earns impressions and is being under-served. Add it.
- Don't inspect a large shortlist to satisfy curiosity. Inspect when the user needs to know *why* a specific page isn't appearing, and spend the smallest budget that answers it.
