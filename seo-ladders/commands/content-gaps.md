# /content-gaps

The buyer questions AI gets asked where your brand is **absent or under-represented** — your highest-leverage things to write next. Drawn from your tracked prompts and the latest monitor run.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/content-gaps | jq '.gaps[]'
```

## How to read it

Each gap: `{ promptId, prompt, intentLabel, brandPct, missing, total, competitors, severity }`.

- **`severity: "missing"`** — AI never names you for this prompt. Highest priority.
- **`severity: "partial"`** — you show up sometimes (`brandPct` < 100). Strengthen it.
- **`competitors`** — who AI names *instead of* you for this question.
- **`brandPct`** — % of answers (across engines) that mention you.

## What to do with the result

- Sort by `severity: "missing"` then lowest `brandPct` — write those first.
- Hand the gap's `prompt` to `/write-article` (it's already a real buyer question), or schedule it with `/content-calendar`.
- If `hasData: false`, there's no completed monitor run yet — tell the user to run the monitor on the dashboard.
- Pair with `/ai-visibility` (the score) and `/actions` (outreach to the sources AI cites).
