# /backlinks

Check Backlink Exchange membership, credit balance, and link targets.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/backlinks | jq '{membership, balance, targets}'
```

## How to read it

- **`membership`** — `{joined, isActive, eligibilityStatus, hint?}`.
  - `joined:false` → not in the exchange. Tell the user to **join at `/dashboard/exchange`**.
  - `isActive:false` or an `eligibilityStatus`/`hint` → surface the `hint` (e.g. DR floor, links owed).
- **`balance`** — `{current_balance, total_credits, total_debits}`. Credits are DR-weighted: give links to earn links.
- **`targets`** — pages you can link to (and the credits each is worth).

## Join the exchange (opt-in)

Enroll the site in the DR-weighted network — you link out from your articles, others link in. All fields are optional.

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"accepted_industries":["saas"],"blocked_domains":["competitor.com"],"max_outbound_links_per_article":2}' \
  https://www.seoladders.com/api/v1/backlinks/join | jq '{joined, membership}'
```

**Confirm with the user before joining** — it enrolls the site in a public link network. Send an empty body `{}` to join with defaults.

## What to do with the result

- If not joined → offer `/backlinks/join` (above) or point the user to `/dashboard/exchange`.
- If joined → list `targets` and feed 1–2 into the next `/write-article` so the article gives a link (earning credits back).
- Low `current_balance` → you owe links; place some via new articles to keep earning. See `references/plans-and-backlinks.md`.
