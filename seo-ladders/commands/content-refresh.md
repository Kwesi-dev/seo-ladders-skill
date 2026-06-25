# /content-refresh

Find articles whose rankings are decaying, then refresh one.

## Find candidates

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/refresh-candidates | jq '.candidates[]'
```

## Refresh one

Pass the candidate's `blogPostId` (from the list above, or a `/content-radar` row's `blogPostId`). Refresh updates an article **we generated** in place, so it's keyed by `blogPostId`, not a URL.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"blogPostId":"BLOG_POST_ID"}' \
  https://www.seoladders.com/api/v1/refreshes | jq .
```

## What to do with the result

- Pick the candidates with the biggest ranking drop / most lost traffic first.
- `refresh` updates an article **we generated** that already ranks but is decaying. It needs a `blogPostId`. For pages buried on page 2, or **pre-join blogs we didn't generate** (no `blogPostId`), use `/optimize` instead (it accepts a `sourceUrl`).
- If the refresh returns an async job id, poll `GET /jobs/{id}` until `completed`.
- `/content-radar` rows with `action: refresh` are the same target set (always internal, so they carry a `blogPostId`).
