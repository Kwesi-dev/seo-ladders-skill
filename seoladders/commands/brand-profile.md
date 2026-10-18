# /brand-profile

What the agent knows about your business, as the Brand Profile page shows it. Free.

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/brand | jq '{identity, local, audiences, internalLinks: {count: .internalLinks.count, sample: .internalLinks.links[:10]}}'
```

(MCP: `brand-profile`.)

- **`identity`**: site, name, industry, target market, description, primary CTA, conversion goal, country, language, primary keyword.
- **`audiences`**: who you sell to, with their search intent and pain points.
- **`internalLinks`**: the pages the writer links to from articles.

Profile changes are made in the dashboard at /dashboard/brand. A fact the writer gets wrong is corrected with `/knowledge`.
