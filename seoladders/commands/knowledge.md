# /knowledge

Your knowledge base: what the agent knows about your business, and what every article is written from. The agent built it from your site at signup and keeps it current. In the dashboard: /dashboard/writer-setup?tab=knowledge. **Optional:** we already scrape your website, so only add facts that *aren't* on it (pricing nuances, positioning, proprietary data, launch details). The Article Writer pulls from this automatically.

## List your knowledge sources

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/knowledge | jq '.sources[] | {id, type, title, status, content: (.content // "" | .[:300])}'
```

`type: "generated"` sections are what the agent built from your site (product, pricing, audience...). The rest are notes, files and URLs you added.

## Correct a section the agent got wrong (free)

```bash
curl -s -X PATCH -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"content":"Pro is $97/mo with a 7-day trial. There is no free plan."}' \
  https://www.seoladders.com/api/v1/knowledge/SOURCE_ID | jq .
```

(MCP: `knowledge` with `editId` + `editContent`.)

- Only `generated` sections. The text replaces the section, is re-embedded so the next article uses it, and is protected: if the site changes later, the dashboard asks before overwriting your correction.
- Show the user the current text and your proposed replacement, and confirm before saving.
- Nothing is deleted from here; removing a source is done in the dashboard.

## Add a fact (note) or a URL

```bash
# A note (raw facts)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"note","title":"Pricing","content":"Pro is $97/mo early-bird, 7-day trial, 5 seats."}' \
  https://www.seoladders.com/api/v1/knowledge | jq '.source | {id, type, status}'

# A URL to ingest
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"type":"url","url":"https://example.com/about"}' \
  https://www.seoladders.com/api/v1/knowledge | jq '.source | {id, type, status}'
```

- Body: `{ type:"note", title?, content }` or `{ type:"url", title?, url }`.
- New sources start `status: "processing"` and become `ready` once ingested.
- Each addition spends **one credit from a monthly knowledge allowance** (Pro: 50/mo). A `429` means it is spent. Add facts the writer is getting wrong, not a copy of the website.

## What to do with the result

- Suggest adding knowledge only when the user has facts the website doesn't state — don't nag (the site is already scraped).
- After adding, those facts are used in every article your agent writes from then on.
