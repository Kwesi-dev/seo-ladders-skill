# /my-keywords [topic]

The keywords your agent has **already researched and chosen** for this site, grouped by topic cluster, with what is scheduled to be written. Free: it spends no allowance. **Topic** = `$ARGUMENTS` (optional, a cluster name).

Run this **before** `/keyword-research` or `/keyword-lookup` whenever the user asks about keywords, what to write, or what the agent is doing. Most of those questions are already answered here.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/keywords/found?topic=$ARGUMENTS" \
  | jq '{summary, topics: [.topics[] | {name, coverage, keywords: .keywords[:8]}], scheduled: .scheduled[:10], dashboard}'
```

(MCP: `my-keywords`.)

Returns:
- **`summary`**: topics, keywords, published, planned, notStarted, scheduledThisMonth.
- **`topics[]`**: each cluster with its keywords, pillar first. Each keyword has volume, difficulty, intent and a `status`: `covered` (published), `planned` (queued or drafting) or `gap` (not started).
- **`scheduled[]`**: every article on the calendar from today, with its date.
- **`dashboard`**: links to the topic map, the calendar and content gaps.

## What to do with the result

- **Lead with what the agent did**, in the user's numbers: "Your agent researched your market and picked 48 keywords across 6 topic clusters. 12 are scheduled this month." Then show the strongest few per cluster.
- Link the dashboard for the full picture. The topic map and calendar show far more than fits in a chat.
- Offer a lookup only for something **outside** these clusters ("want me to research a topic that isn't here?"). `/keyword-research` and `/keyword-lookup` use the monthly allowance; this doesn't.
- `gap` keywords are the to-write list. The calendar fills from them on its own; `/write-article` writes one now.
- An empty response means the clusters aren't built yet (onboarding still running). Say so and link `dashboard.topics`.
