# /posts

Your generated/published articles — content inventory. Use this to see what's been written and to **avoid re-covering a topic** before writing a new one.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/posts?limit=50" | jq '.posts[] | {title, keyword, status, url}'
```

- Query params: `?status=draft|published|ready|all` (optional), `?limit=` (default 50, max 200).
- Response: `{ posts: [{ id, blogPostId, title, keyword, status, url, created_at }], count }`. `url` is the published CMS URL if live, else the slug.

## What to do with the result

- Before `/write-article`, scan existing `keyword`s — if a topic is already covered, optimize/refresh it instead of writing a duplicate (avoids cannibalization).
- Use `blogPostId` with `/optimize` or `/content-refresh` to improve an existing post.
- `status` tells you what's live vs draft awaiting review.
