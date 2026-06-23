# /prompts

List, discover, and add the AI prompts (buyer questions) you track for AI visibility. Cap-aware.

## List what you track + your cap

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/prompts | jq '{used, cap, remaining, prompts}'
```

- `used` / `cap` / `remaining` — your plan's prompt budget (Pro = 30).
- `prompts` — `[{id, text, intent, isActive}]`.

## Get suggestions to add

```bash
# source = gsc (your real Search Console queries) | keywords | paa (People-Also-Asked)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/prompts/explorer?source=gsc" | jq '{sourceLabel, suggestions, note, needsConnect}'
```

- Prefer **`source=gsc`** — prompts derived from queries you already rank for convert best.
- `needsConnect:true` (gsc) → GSC isn't connected; fall back to `source=keywords` or `source=paa`, and tell the user to connect GSC.
- Pass `&seed=<topic>` to focus `keywords`/`paa`.

## Add prompts (cap-aware)

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"prompts":["best crm for startups","crm with ai features"]}' \
  https://www.seoladders.com/api/v1/prompts | jq '{added, skipped, used, cap, remaining, message}'
```

## What to do with the result

- `added` went in; `skipped` did not.
- If `remaining` is 0, new prompts come back in **`skipped`**. Tell the user they're at cap — to swap, deactivate/remove a low-value prompt on the dashboard first, then re-add.
- Prioritize high-intent buyer questions where `/ai-visibility` shows you're absent.
