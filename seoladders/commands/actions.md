# /actions

Fetch prioritized recommendations drawn from your real monitoring data — outreach, Reddit, content gaps, setup.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/actions?status=suggested" \
  | jq '{recommended: [.recommended[]? | {severity, title, description, url}], outreachWaiting, workDone, count, actions: [.actions[] | {priority, type, title, body}], dashboardUrl}'
```

## How to read it

- **`recommended`**: the action cards on the dashboard's Actions page, in the same order: site audit issues, AI stating wrong facts (with the page that likely still says it), buying-journey stages with no page for AI to cite, and more. Each has a dashboard `url`.
- **`outreachWaiting`**: outreach the agent drafted that's waiting for the user to send.
- **`workDone`**: what the agent did since the last scan. Lead with it.


- `actions` — `[{id, type, priority, title, body}]`.
- **`type`**:
  - `outreach` — earn a mention/link on a source AI or Google already trusts.
  - `reddit` — answer a relevant thread where your buyers are.
  - `content` — a content gap. Blog-style gaps are written by the agent through the calendar on their own; page-style ones (pricing, comparison) need the owner.
  - `setup` — finish a connection/config (e.g. connect GSC).
- `priority` — higher first.

## What to do with the result

- Sort by `priority` and present the top 3–5 with their `body`.
- Route each: `content` → `/content-calendar` if it is not already queued, `setup` → `/seoladders-setup`, `outreach`/`reddit` → hand the user the target + angle from `body`.
- These actions are how you close the gaps `/ai-visibility` surfaces. See `references/ai-visibility-playbook.md`.
