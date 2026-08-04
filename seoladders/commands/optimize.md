# /optimize <url>

Rewrite a page stuck on page 2+ using its real Google Search Console data.

**Page URL** = `$ARGUMENTS`. If `$ARGUMENTS` is empty, pick a `striking_distance`, `low_ctr`, `declining` or `page_two_plus` URL from `/content-radar` (or a position 11–20 row from `/rankings`) and confirm with the user. **Never pick an `underperforming` row** — those are buried past position 40, where a rewrite rarely helps and the right call is merge or prune.

Pass the row's `bucket` too, when it came from Content Radar. It tells the optimizer *why* the page was flagged and changes what it does: a `low_ctr` page gets its title and meta rewritten and its body left alone (the content is what earned the ranking), where a `page_two_plus` page gets a genuine rebuild. Without it the optimizer treats every page identically.

You must pass **both** the `keyword` and a source. Take them straight from the Content Radar row: use its `primaryKeyword` as `keyword`, and either its `url` (as `sourceUrl` — works for any page, incl. pre-join blogs) **or** its `blogPostId` (for a page we generated).

```bash
# By URL (any page, incl. pre-join blogs) — the common case
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keyword":"PRIMARY_KEYWORD","sourceUrl":"$ARGUMENTS"}' \
  https://www.seoladders.com/api/v1/optimizations | jq .

# Or, for a page we generated, pass blogPostId instead of sourceUrl:
# -d '{"keyword":"PRIMARY_KEYWORD","blogPostId":"BLOG_POST_ID"}'
```

## What to do with the result

- Use this on `striking_distance`, `low_ctr`, `declining` or `page_two_plus` rows from `/content-radar`, or `position` 11–20 keywords from `/rankings`. Positions 5–20 are where refresh work pays fastest.
- It pulls the page's GSC queries and rewrites for the terms it already half-ranks for — the fastest path to page 1.
- Async → returns `{ optimizationId }`. Poll `GET /api/v1/optimizations/{optimizationId}` until `rewriteStatus` is `ready` — then `rewrittenPostId` is the finished, publishable optimized article (open `rewrittenArticle.dashboardUrl`, or fetch full content via `/posts` / the article endpoint). `rewriteStatus: rewriting` = still working; `failed` = read `rewriteError`.
- The response also carries the analysis (`score`, `suggestions`) — but you don't need to apply anything; the rewrite already fixed the gaps.
- Decaying articles that already rank are also handled here — optimize refreshes them in place.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/optimizations/OPTIMIZATION_ID | jq '{status, rewriteStatus, rewrittenPostId, rewrittenArticle}'
```
