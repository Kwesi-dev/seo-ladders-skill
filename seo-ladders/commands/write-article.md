# /write-article <keyword>

Research → draft → media → FAQ → citations → schema → internal links, and publish one article. Async.

**Keyword** = `$ARGUMENTS`. If `$ARGUMENTS` is empty, pull a target from `/content-gaps` or `/keyword-research` first, then confirm it with the user.

## Kick it off

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"$ARGUMENTS"}' \
  https://www.seoladders.com/api/v1/articles | jq .
```

Returns an async batch/job id.

## Poll until done

```bash
# Poll the job
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/jobs/JOB_ID | jq '{status, output, error}'

# Or fetch the article once it exists
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/ARTICLE_ID | jq .
```

- Poll every ~10s until `status` is `ready` (article generation takes a while — it's a full pipeline).
- Once ready, `GET /v1/articles/{id}` returns the finished article:
  - **`articleMarkdown`** — full article as Markdown
  - **`htmlContent`** — complete standalone HTML document (head, meta, JSON-LD, table of contents, styled layout, YouTube embeds) — the exact dashboard "Export HTML"
  - **`articleContentJson`** — structured sections / FAQ / media plan
  - **`jsonLd`** — schema; plus `title`, `slug`, `metaDescription`, `wordCount`, `externalUrl` (live URL if published)

## What to do with the result

- Save `htmlContent` to a `.html` file (it's self-contained), or `articleMarkdown` to `.md` — both are publish-ready.
- The article already ships with internal links, AI images, YouTube embeds, citations, and 1–2 backlink-exchange links (when available) — check exchange targets with `/backlinks`.
- To push it live, use `/publish <article-id>` (or it auto-publishes if you enabled auto-publish).
- Pro includes 20 articles/mo. Write for keywords from `/keyword-research` and gaps from `/ai-visibility`.
