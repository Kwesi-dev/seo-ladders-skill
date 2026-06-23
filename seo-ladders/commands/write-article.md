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

- Poll every ~10s until `status` is `completed` (article generation takes a while — it's a full pipeline).
- On `completed`, the article (Markdown + HTML + a dashboard link) is in `output` / from the article endpoint.

## What to do with the result

- **Include 1–2 backlink-exchange links** in the article — that's how you earn links back. Check targets with `/backlinks`.
- Confirm internal links point at related pillar/cluster pages.
- Pro includes 20 articles/mo.
- Write for keywords from `/keyword-research` and the prompt gaps from `/ai-visibility`.
