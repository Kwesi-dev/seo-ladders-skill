# /write-article

The article writer. **The agent already writes for you.** It schedules your keywords on the AutoBlog calendar and, on each scheduled day, researches the topic, writes the article (sources, images, internal links, FAQ, schema) and publishes it. So this command starts by showing that plan. Usually the answer is "it's on its way".

## Show the plan first (free)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/today \
  | jq '{plan, articles, today, upcoming: .upcoming[:7], writingNow, calendarUrl, instructions_for_agent}'
```

(MCP: `write-article` with no args.)

- `plan`: `trial` or `pro`. `articles`: used, limit and remaining this billing cycle.
- `today[]`: what is scheduled today and its state (`pending` = not started, `in_progress`, `published`, `failed`), with a link once written.
- `upcoming[]`: the next scheduled articles with their dates.
- `calendarUrl`: the AutoBlog calendar.

Tell the user, in their numbers:

> Your agent writes your articles for you. On your trial it researches, writes and publishes 3 articles, one on each scheduled day. Next up: "best crm for startups" today, "crm vs spreadsheet" on Oct 21. See the full schedule: [calendar].

On Pro, say how many it writes this cycle and how many are left. Then link the articles already written so they can read them.

## Writing today's article now

Only **today's** scheduled article can be written early, and only if the agent hasn't started it. One article, never several, never a keyword that isn't on today's calendar.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{}' \
  https://www.seoladders.com/api/v1/articles/today | jq '{articleId, keyword, dashboardUrl, message}'
```

(MCP: `write-article` with `write: true`.)

- It is the same article the daily run would write, so it is never written twice, and it uses the same article allowance.
- Refusals come back as `409` with `instructions_for_agent`: `nothing_scheduled_today` (with the next date), `not_todays_article`, `already_written` (with its link) or `already_writing`. Read the message to the user; don't retry.
- Poll `GET /api/v1/articles/ARTICLE_ID` (MCP: `get-article`) until `status` is `ready`.

## Want a different article?

Change the schedule rather than writing around it: `/content-calendar` adds or moves a keyword, and the agent writes it on its date. `/my-keywords` shows everything it has chosen.
