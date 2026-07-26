# /knowledge

Your knowledge base — facts that ground article writing. **Optional:** we already scrape your website, so only add facts that *aren't* on it (pricing nuances, positioning, proprietary data, launch details). The Article Writer pulls from this automatically.

## List your knowledge sources

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/knowledge | jq '.sources[] | {type, title, status}'
```

## Add a fact (note) or a URL

```bash
# A note (raw facts)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"note","title":"Pricing","content":"Pro is $99/mo early-bird, 3-day trial, 2 seats."}' \
  https://www.seoladders.com/api/v1/knowledge | jq '.source | {id, type, status}'

# A URL to ingest
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"url","url":"https://example.com/about"}' \
  https://www.seoladders.com/api/v1/knowledge | jq '.source | {id, type, status}'
```

- Body: `{ type:"note", title?, content }` or `{ type:"url", title?, url }`.
- New sources start `status: "processing"` and become `ready` once ingested.

## What to do with the result

- Suggest adding knowledge only when the user has facts the website doesn't state — don't nag (the site is already scraped).
- After adding, those facts are used the next time you `/write-article`.
