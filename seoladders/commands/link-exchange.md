# /link-exchange

Your Link Exchange status, read-only. Members' articles place a relevant link to each other's sites; this shows yours. Free.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/exchange | jq '{member, credits, readiness, counts, received: .links.received[:10], placed: .links.placed[:10]}'
```

(MCP: `link-exchange`.)

- **`member`**: whether the site has joined. If not, joining happens in the dashboard at /dashboard/link-building/exchange; never offer to join from chat.
- **`credits`**: balance, earned, spent, and whether the site can receive links right now.
- **`readiness.items`**: what's left to do before the site is fully set up for the exchange, each with a link.
- **`links.received` / `links.placed`**: links placed for you inside members' articles, and links your articles placed for others, each with its live status.

Describe it as links **placed** inside relevant articles. Don't state how many members there are, how many links to expect, or how fast.
