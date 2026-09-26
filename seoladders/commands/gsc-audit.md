# /gsc-audit <domain>

Full SEO audit — health score, CTR, decay, page-2, technical issues.

**Read the stored audit first.** The site is re-crawled automatically every week, and a
new crawl is billed per page *and* metered against the site-audit allowance. Only start
one when the stored audit predates changes the user has since made — and say so before
you do.

**Domain** = `$ARGUMENTS`. If `$ARGUMENTS` is empty, send `{}` to audit your active site.

## Start a new audit (only when the stored one is out of date)

```bash
curl -s -X POST -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  -H "Content-Type: application/json" -d '{"domain":"$ARGUMENTS"}' \
  https://www.seoladders.com/api/v1/audit | jq '{auditId, status}'
```

`domain` is optional — omit it (or send `{}`) to audit your active site.
Returns `{ auditId, status: "analyzing" }`. A `429` means the site-audit allowance for
the cycle is spent; surface the message rather than retrying.

## Poll that audit until it is ready

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  "https://www.seoladders.com/api/v1/audit?id=AUDIT_ID" | jq '{status, health_score, total_issues}'
```

- Poll every ~10s until `status` is `ready` (about 1-3 minutes).
- There is no job queue and no `/jobs/{id}` endpoint for this. Poll the audit itself.

## Or read the latest stored audit (no wait)

```bash
curl -s -H "Authorization: Bearer $SEO_LADDERS_API_KEY" \
  https://www.seoladders.com/api/v1/audit | jq '.audit'
```

Returns `{ audit: {id, status, health_score, total_issues, total_pages_crawled, ...} | null }`. `null` → no audit yet, run one.

## What to do with the result

- Report `health_score`, `total_issues`, `total_pages_crawled`.
- **Read the stored audit first (`GET`), don't start a new one.** Every crawl is billed per page and the audit re-runs weekly on its own, so a fresh `POST` is only warranted when the stored audit predates changes the user has since made. There is no manual scan in the dashboard any more, for exactly this reason.
- Pair with `/content-radar` to route pages to optimize. See `references/audit-playbook.md`.
- When the user marks an issue fixed, the page is **re-checked live** — a fix that did not land comes back as `not_verified` rather than silently closing. Tell them, so they know the tick is verified rather than taken on trust.
