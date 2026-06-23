# /actions

Fetch prioritized recommendations drawn from your real monitoring data — outreach, Reddit, content gaps, setup.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/actions?status=suggested" | jq '{count, actions: [.actions[] | {priority, type, title, body}]}'
```

## How to read it

- `actions` — `[{id, type, priority, title, body}]`.
- **`type`**:
  - `outreach` — earn a mention/link on a source AI or Google already trusts.
  - `reddit` — answer a relevant thread where your buyers are.
  - `content` — a content gap to write or expand (`/write-article`).
  - `setup` — finish a connection/config (e.g. connect GSC).
- `priority` — higher first.

## What to do with the result

- Sort by `priority` and present the top 3–5 with their `body`.
- Route each: `content` → `/write-article`, `setup` → `/seo-ladders-setup`, `outreach`/`reddit` → hand the user the target + angle from `body`.
- These actions are how you close the gaps `/ai-visibility` surfaces. See `references/ai-visibility-playbook.md`.
