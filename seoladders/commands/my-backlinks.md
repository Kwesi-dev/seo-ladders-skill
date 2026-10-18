# /my-backlinks

The links pointing at **your business site**, and where each one came from. The agent keeps this profile up to date every week, so reading it is free.

```bash
# The profile the agent already tracks (free)
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/backlinks?limit=50" | jq '{summary, domains: .domains[:15], exchange, dashboardUrl}'

# A fresh check right now: domain rating, totals, dofollow share, spam score (metered)
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/backlinks | jq '{domainRating, backlinks, referringDomains, dofollowPercent, spamScore}'
```

(MCP: `my-backlinks`; `live: true` for the fresh check.)

- **`summary`**: referring sites, links, how many came from outreach the agent drafted, how many from prospects that linked before being pitched, organic, links placed through the link exchange, and when it was last synced.
- **`domains[]`**: each referring site's link, anchor, dofollow, authority, and `cameFrom` (`outreach_page`, `outreach_domain`, `prospect_unpitched`, `organic`).
- **Only your site.** There is no domain argument.
- The fresh check is **metered**: one competitor gap unit from the dashboard's allowance, and a check from the last week is served from cache for free. The tracked profile is usually enough.

## What to do with the result

- Lead with the tracked numbers and the links outreach earned. Link `dashboardUrl` for the full list.
- For the next pages worth pitching, go to `/link-building`: the agent already found them and drafted the outreach.
