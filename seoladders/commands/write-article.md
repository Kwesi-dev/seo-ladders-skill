# /write-article <keyword-or-topic>

Research → draft → media → FAQ → citations → schema → internal links, and publish one article. Async.

**Target** = `$ARGUMENTS` — a keyword **or a raw topic / question** (e.g. a Content Gap prompt like "how does a search engine match keywords to a page"). No keyword research required: the pipeline studies the top SERP results for whatever you pass and auto-picks the format. If `$ARGUMENTS` is empty, pull a target from `/content-gaps` or `/keyword-research` first, then confirm it with the user.

## Kick it off

The target — keyword or raw topic — goes in the `keyword` field (it's free-text):

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"keyword":"$ARGUMENTS"}' \
  https://www.seoladders.com/api/v1/articles | jq .
```

Optional fields on the same body:

- **`articleType`** — `"Explainer"`, `"Guide: How-to"` or `"Listicle"`. Pick it from the
  search intent rather than leaving it to default.
- **`subDescription`** — an angle or audience note to steer the outline.
- **`referenceUrl`** — a page to ground the article in.

Returns `{ articleId, status, dashboardUrl }`. **`dashboardUrl`** is an SEOLadders link to watch/edit the article (`…/dashboard/article-writing?articleId=…`) — share it with the user so they can follow generation and edit in the dashboard.

## Poll until done

```bash
# Poll the ARTICLE — this endpoint returns no job id, so there is nothing to poll at
# /jobs/{id} — there is no such endpoint; a site audit is polled at
# /audit?id=<auditId>.
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/ARTICLE_ID | jq .
```

- Poll every ~10s until `status` is `ready` (article generation takes a while — it's a full pipeline).
- Once ready, `GET /v1/articles/{id}` returns the finished article:
  - **`articleMarkdown`** — full article as Markdown
  - **`htmlContent`** — complete standalone HTML document (head, meta, JSON-LD, table of contents, styled layout, YouTube embeds) — the exact dashboard "Export HTML"
  - **`articleContentJson`** — structured sections / FAQ / media plan
  - **`jsonLd`** — schema; plus `title`, `slug`, `metaDescription`, `wordCount`, `externalUrl` (live URL if published), and **`dashboardUrl`** (the SEOLadders link to view/edit it)

## What to do with the result

- Save `htmlContent` to a `.html` file (it's self-contained), or `articleMarkdown` to `.md` — both are publish-ready.
- The article ships with internal links, AI images and citations. A video is added **only where one genuinely fits a section**, so expect none, one or two — and never a competitor's or another vendor's channel. Do not promise embeds up front.
- To push it live, use `/publish <article-id>` — though with a CMS connected it auto-publishes on its own, since auto-publish is on by default.
- Pro includes 30 articles/mo. Write for keywords from `/keyword-research` and gaps from `/ai-visibility`.
