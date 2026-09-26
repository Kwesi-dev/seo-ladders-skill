# /publish <article-id>

Publish a generated draft to your connected CMS now.

**Article id** = `$ARGUMENTS`.

## Publish it

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/articles/$ARGUMENTS/publish | jq .
```

Returns `{published, url?, externalId?}`.

For a rewrite that came from `/optimize` with a `sourceUrl` (a page we did not write),
add `replacesOriginal` **only after the user confirms the original page is down** —
see the 409 below:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"replacesOriginal":true}' \
  https://www.seoladders.com/api/v1/articles/$ARGUMENTS/publish | jq .
```

## Before you call

- Check `/integrations` first — you need a **connected CMS** (WordPress or webhook). `400` if none is connected.
- The article must be **`ready`**. `400` if it isn't, `404` if it's not your post.
- **`409 replaces_pre_join_page`** — the draft is a rewrite of a page we never wrote,
  so publishing it would create a second page competing with the user's own live one.
  Do not route around this. Hand the content to the user to paste over the existing
  post, and use `replacesOriginal: true` only once they confirm the original is down.

## Hands-off (this is the default)

Auto-publish is **already on** once a CMS is connected, so finished articles push to
the CMS on their own. Use this endpoint to read it, or to send `false` and put the
account into review mode:

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"enabled":true}' \
  https://www.seoladders.com/api/v1/settings/auto-publish | jq .
```

> **Both of these are ON by default.** Connecting a CMS switches auto-publish on by
> itself, and autofill ships enabled for every account. Read the current value before
> saying anything to the user: telling them to "turn on" something already running,
> or that nothing will publish until they do, is wrong. Review mode is the *switch* —
> send `{"enabled": false}` to hold articles as drafts for approval. Confirm before
> CHANGING either setting in either direction.

`400` if no CMS is connected.
