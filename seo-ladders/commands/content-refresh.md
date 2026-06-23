# /content-refresh

Find articles whose rankings are decaying, then refresh one.

## Find candidates

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/refresh-candidates | jq '.candidates[]'
```

## Refresh one

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"url":"https://example.com/post"}' \
  https://www.seoladders.com/api/v1/refreshes | jq .
```

## What to do with the result

- Pick the candidates with the biggest ranking drop / most lost traffic first.
- `refresh` updates an article that already ranks but is decaying. For pages buried on page 2 that never broke through, use `/optimize` instead.
- If the refresh returns an async job id, poll `GET /jobs/{id}` until `completed`.
- `/content-radar` rows with `action: refresh` are the same target set.
