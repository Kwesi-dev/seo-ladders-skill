# /overview

The dashboard home page in chat: what your agent did lately and where the site stands. Free.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/overview | jq '{wins, kpis, articles, audit, activity: .activity[:8], dashboardUrl}'
```

(MCP: `overview`.)

- **`wins`**: recent wins (new rankings, AI mentions, published articles).
- **`kpis`**: AI Visibility Score and its change; Search Console clicks and impressions from the stored weekly data; ranked keywords; domain rating (`stale` when over 30 days old).
- **`articles`**: total, published, scheduled.
- **`audit`**: the latest site audit: score, pages scanned, top high-priority issues.
- **`activity`**: what the agent did, newest first, with links.

Start here when the user asks "how's it going?". For website visits use `/analytics`; for live search numbers, `/search-console`.
