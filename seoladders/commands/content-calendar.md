# /content-calendar

List and schedule AutoBlog articles.

## List the calendar

```bash
# Optional ?month=YYYY-MM to scope to one month
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/calendar?month=2026-07" \
  | jq '{count, entries: [.entries[] | {keyword, article_type, status, scheduled_date, search_volume, keyword_difficulty, intent}]}'
```

`entries` — `[{id, keyword, article_type, status, search_volume, keyword_difficulty, intent, scheduled_date, created_at}]`.

## Schedule an article

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"keyword":"how to rank on chatgpt","date":"2026-07-01"}' \
  https://www.seoladders.com/api/v1/calendar | jq '.entry'
```

`keyword` is required. `date`, `status`, `article_type` are optional.

## Autofill (on by default)

AutoBlog auto-schedules ~a month of DR-matched keywords each cycle, so the calendar never runs dry:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"enabled":true}' \
  https://www.seoladders.com/api/v1/autoblog/autofill | jq .
```

Auto-publish is likewise on once a CMS is connected, so generate-and-ship is already the
default path.

> **Both of these are ON by default.** Connecting a CMS switches auto-publish on by
> itself, and autofill ships enabled for every account. Read the current value before
> saying anything to the user: telling them to "turn on" something already running,
> or that nothing will publish until they do, is wrong. Review mode is the *switch* —
> send `{"enabled": false}` to hold articles as drafts for approval. Confirm before
> CHANGING either setting in either direction.


## What to do with the result

- Use it to space out publishing instead of writing everything at once.
- Schedule keywords from `/keyword-research` and the prompt gaps from `/ai-visibility`.
- To write a scheduled keyword now, hand it to `/write-article`.
