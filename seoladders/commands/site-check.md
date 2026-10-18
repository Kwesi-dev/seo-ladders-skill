# /site-check <check> [url]

Quick technical checks on **your** site. **Check** = the first word of `$ARGUMENTS`; an optional URL after it must be a page or sitemap on your site (it defaults to the homepage).

| check | What it tells you |
|---|---|
| `speed` | Core Web Vitals and the top fixes, from Google PageSpeed (`strategy`: mobile or desktop) |
| `ai-readiness` | Whether AI crawlers can reach and read the site: robots.txt rules per AI bot, llms.txt, structured data |
| `llms-txt` | A ready-to-upload llms.txt built from your pages |
| `sitemaps` | Every sitemap the site exposes and how many URLs each holds |
| `sitemap-validate` | Errors and warnings in one sitemap (pass the sitemap URL) |
| `eeat` | E-E-A-T signals on one page: author, sources, trust pages |

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"check":"ai-readiness"}' \
  https://www.seoladders.com/api/v1/site-check | jq .
```

(MCP: `site-check`.)

- **Not metered.** These read your public pages directly. An llms.txt build is kept for a week, so asking again returns the same file. The 60-a-minute request budget still applies.
- This is not the full site audit. The agent crawls the whole site and re-audits it every week: read that with `/gsc-audit` first.

## What to do with the result

- Fix `fail` items before `warn` items. For `llms-txt`, hand the user the file to upload to the site root.
- If the full audit already flags the same issue, say so and link the audit rather than repeating it.
