# /competitors

Manage the competitors you track for AI visibility (share-of-voice). You get **5 slots**. Suggestions are brands AI named in answers that you don't track yet.

`$ARGUMENTS` = optional competitor domain(s) to add. If empty, just list tracked + suggestions and let the user pick which to add.

## List tracked + suggestions + slots

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/competitors | jq '{slots, tracked, suggestions}'
```

- Returns `{ tracked, ignored, suggestions: [{name,count}], slots: {used, cap, remaining} }`.
- `tracked` is **domain-first** — a competitor stored with a known domain comes back as `jasper.ai`, one only ever seen as a brand name comes back as that name.
- `suggestions` are ranked by how often AI named them — the best ones to promote.
- `slots.remaining` is how many more you can add (cap is 5).

## Add a competitor (cap-aware — promote a suggestion or add your own)

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"names":["competitor.com","rival.io"]}' \
  https://www.seoladders.com/api/v1/competitors | jq '{added, skipped, slots}'
```

- Body: `{ name: string }` or `{ names: string[] }`. Either form works — a domain (`jasper.ai`) or a brand name (`Jasper`). They're matched on the same identity, so adding one after the other returns `already_tracked` rather than burning a second slot. Removal accepts either form too.
- Over the 5-slot cap → the extras come back in `skipped` with `reason: "cap_reached"`; already-tracked ones come back as `already_tracked`.

## Remove a competitor (free up a slot)

```bash
curl -s -X DELETE -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/competitors?name=rival.io" | jq '{tracked, slots}'
```

## What to do with the result

- Show `slots.used/cap`. If `remaining` is 0, tell the user to remove one before adding another.
- Recommend promoting the top `suggestions` (highest `count`) — those are who AI actually mentions instead of (or alongside) the brand.
- After changing competitors, re-run the monitor on the dashboard so share-of-voice updates. See `/ai-visibility`.
