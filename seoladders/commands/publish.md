# /publish <article-id>

Publish a generated draft to your connected CMS now.

**Article id** = `$ARGUMENTS`.

## Publish it

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/$ARGUMENTS/publish | jq .
```

Returns `{published, url?, externalId?}`.

## Before you call

- Check `/integrations` first — you need a **connected CMS** (WordPress or webhook). `400` if none is connected.
- The article must be **`ready`**. `400` if it isn't, `404` if it's not your post.

## Hands-off

To never publish manually, enable **auto-publish** — finished articles push to the CMS automatically:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"enabled":true}' \
  https://www.seoladders.com/api/v1/settings/auto-publish | jq .
```

**Confirm with the user first.** This pushes content live without review (`400` if no CMS is connected).
