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

Each gap is a real buyer question. The agent schedules gaps onto the calendar on its own; check `/write-article` for what's already coming before adding one. To add one yourself, schedule it, or mark it handled.

```bash
# Schedule it onto the AutoBlog calendar (prompt = raw topic); the agent writes it on that date
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
- **AutoBlog already schedules blog-format gaps on its own**, about two a week (8 of a 30-day calendar), worst first, when it fills each month's calendar. Nothing is swapped in mid-month: a gap found after the fill waits for the next one. Check `/content-calendar` before scheduling a gap by hand so you don't add it twice. Page-format gaps (comparison, pricing) are never scheduled automatically.
- After you write or schedule a gap, **mark it done** (`status: "done"`) so it leaves the list and shows as handled in the Actions kanban. Reopen with `status: "reopen"`.
- If `hasData: false`, there's no completed monitor run yet — the monitor is created for you during onboarding and reruns on a schedule throughout the week (a trial gets one scan), so this means the first run has not landed yet — not that the user must do something. **Pro has no manual runs**, so do not send them to a Run button: it will refuse them.
- Pair with `/ai-visibility` (the score) and `/actions` (outreach to the sources AI cites).
