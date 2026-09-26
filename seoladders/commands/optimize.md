# /optimize <url>

Rewrite a page stuck on page 2+ using its real Google Search Console data.

**This is metered.** Every call spends one optimize/refresh credit from a small
monthly allowance shared with refresh. Never loop over a Content Radar list: pick the
single highest-value page and confirm with the user first. A `429` means the allowance
is spent for the cycle.

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
- Async → returns `{ optimizationId }`. Poll `GET /api/v1/optimizations/{optimizationId}` until `rewriteStatus` is `ready` — then `rewrittenPostId` is the finished rewrite (open `rewrittenArticle.dashboardUrl`, or fetch full content via `/posts` / the article endpoint). **Whether you may publish it depends on how you started the job — see the warning below.** `rewriteStatus: rewriting` = still working; `failed` = read `rewriteError`.
- The response also carries the analysis (`score`, `suggestions`) — but you don't need to apply anything; the rewrite already fixed the gaps.
- A decaying page **we generated** is routed to `refresh`, not `optimize` — Content Radar says so in the row's `action`. Nothing is "refreshed in place" for a page we did not write, because we hold no copy of it.

> ### Do not publish a `sourceUrl` rewrite over the top of a live page
>
> This is the one way this command can hurt the user, and it applies to the
> `sourceUrl` path above — the common one.
>
> When you start from `sourceUrl`, SEOLadders holds **no copy** of that page, so the
> rewrite **replaces nothing**. Publishing it creates a SECOND page for the same
> query, and the two then compete — the user's own pages, splitting their own ranking.
>
> `POST /articles/{id}/publish` refuses these with **`409 replaces_pre_join_page`**.
> That refusal is correct; do not route around it. Hand the content to the user to
> paste over their existing post, which keeps the URL, its backlinks and its rankings.
>
> Only once the user confirms the original has been taken down should you retry with
> `{"replacesOriginal": true}`.
>
> A `blogPostId` rewrite has none of this problem: we wrote that page, so the rewrite
> genuinely replaces it.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/optimizations/OPTIMIZATION_ID | jq '{status, rewriteStatus, rewrittenPostId, rewrittenArticle}'
```
