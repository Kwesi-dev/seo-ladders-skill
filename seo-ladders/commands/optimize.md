# /optimize <url>

Rewrite a page stuck on page 2+ using its real Google Search Console data.

**Page URL** = `$ARGUMENTS`. If `$ARGUMENTS` is empty, pick a `page_two_plus` / `striking_distance` URL from `/content-radar` (or a position 11–20 row from `/rankings`) and confirm with the user.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"url":"$ARGUMENTS"}' \
  https://www.seoladders.com/api/v1/optimizations | jq .
```

## What to do with the result

- Use this on `page_two_plus` / `striking_distance` rows from `/content-radar`, or `position` 11–20 keywords from `/rankings`.
- It pulls the page's GSC queries and rewrites for the terms it already half-ranks for — the fastest path to page 1.
- If it returns an async job id, poll `GET /jobs/{id}` until `completed`.
- For decaying articles that already rank, use `/content-refresh` instead.
