# /content-gaps

The buyer questions AI gets asked where your brand is **absent or under-represented** — your highest-leverage things to write next. Drawn from your tracked prompts and the latest monitor run.

```bash
# List: open gaps in `gaps`, already-handled ones in `done`
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/ai-visibility/content-gaps | jq '{count, gaps, done}'
```

## How to read it

Returns `{ hasData, monitorId, gaps, done, count }`. Each open gap: `{ promptId, prompt, intentLabel, brandPct, missing, total, competitors, severity }`. `done` is the gaps already handled (`{ promptId, prompt, competitors, resolvedAt }`).

- **`severity: "missing"`** — AI never names you for this prompt. Highest priority.
- **`severity: "partial"`** — you show up sometimes (`brandPct` < 100). Strengthen it.
- **`competitors`** — who AI names *instead of* you for this question.
- **`brandPct`** — % of answers (across engines) that mention you.

## Act on a gap

Each gap is a real buyer question — write for it, schedule it, or mark it handled.

```bash
# Write now — the gap's `prompt` is the raw topic (no keyword research needed)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"PROMPT"}' \
  https://www.seoladders.com/api/v1/articles | jq .

# Or schedule it onto the AutoBlog calendar (prompt = raw topic), then mark done
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"PROMPT","date":"2026-07-01"}' \
  https://www.seoladders.com/api/v1/calendar | jq .

# Mark a gap done (leaves the list + lands in the Actions Done column) / reopen
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"promptId":"PROMPT_ID","status":"done"}' \
  https://www.seoladders.com/api/v1/ai-visibility/content-gaps | jq .
# status: "reopen" sends a done gap back to the list.
```

## What to do with the result

- Sort by `severity: "missing"` then lowest `brandPct` — write those first.
- After you write or schedule a gap, **mark it done** (`status: "done"`) so it leaves the list and shows as handled in the Actions kanban. Reopen with `status: "reopen"`.
- If `hasData: false`, there's no completed monitor run yet — tell the user to run the monitor on the dashboard.
- Pair with `/ai-visibility` (the score) and `/actions` (outreach to the sources AI cites).
